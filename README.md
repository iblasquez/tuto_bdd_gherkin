# Le Gherkin, la(es) classe(s) !

Le langage **Gherkin** permet d'écrire des scénarii de test rédigés à l'aide de mots clés tels que **`Given`**, **`When`**, **`Then`** (`Etant donné`, `Quand`, `Alors`) dans un langage naturel lisible et compréhensible par tous (notamment par les gens côté métier). A ce titre, Gherkin est considéré comme un **DSL** (**D**omain **S**pecific **L**anguage).
Martin Fowler qualifie même ce genre de DSL de **[BusinessReadableDSL](https://martinfowler.com/bliki/BusinessReadableDSL.html)**, un langage qui permet de décrire le comportement d'un système sans détailler comment ce comportement est implémenté. 

**Gherkin** est, à la base, le langage utilisé par **Cucumber**, un framework de tests fonctionnels automatisés dont le site de référence est **[cucumber.io](https://cucumber.io/)**. 

Pour découvrir et comprendre la grammaire du langage Gherkin, nous utiliserons des exemples extraits :
- du wiki de Cucumber : [https://github.com/cucumber/cucumber/wiki](https://github.com/cucumber/cucumber/wiki)    
-  de la section Gherkin du manuel Cucumber de référence : [https://cucumber.io/docs/reference#gherkin](https://cucumber.io/docs/reference#gherkin)      
-  et du livre **[The Cucumber Book – Behaviour Driven Development for Testers and Developers de Matt Wyne et Aslak Helesoy](https://pragprog.com/book/hwcuc/the-cucumber-book)**  
<img src="https://images-na.ssl-images-amazon.com/images/I/51dKVQPJolL._SX415_BO1,204,203,200_.jpg" alt="Cucumber Book" width="70">

Grâce à ces différents exemples, nous procéderons pas à pas à la modélisation du **langage Gherkin** sous forme de diagramme de classes à partir des étpaes suivantes :  

* [Le scénario au cœur du langage Gherkin](#scenarioDeBase)
* [Ecrire un scénario paramétré, c'est possible ?](#scenarioParametre)
* [Factoriser des étapes communes à différents scénarii, c'est possible ?](#background)
* [Des scénarii, mais pourquoi ? ... pour décrire une fonctionnalité !](#feature)
* [Et où écrit-on le tout ? ... dans un document Gherkin !](#gherkinDocument)
* [Des tags pour finir...](#tag)
* [Les yeux dans le code : de la conception à l'implémentation Java](#yeuxDansCode)


## 1. Le scénario au cœur du langage Gherkin <a id="scenarioDeBase"></a>

Le langage Gherkin permet de formaliser l'écriture d'un scénario de test.

Un scénario permet d'illustrer une règle métier via un exemple décrit pas à pas (ou plutôt devrions nous dire étape par étape).

Pour comprendre comment écrire simplement un scénario en Gherkin, nous allons examiner le scénario suivant :

```gherkin

Scenario: eat 5 out of 12  
  Given there are 12 cucumbers  
  When I eat 5 cucumbers  
  Then I should have 7 cucumbers  
```

Un ***scénario*** est introduit par un mot-clé (ici `Scenario`) qui peut être suivi ou non :  

-	du nom du scénario : une chaine qui décrit la règle métier que le scénario est censé illustrer (ici `eat 5 out of 12`)    
-	d'une description optionnelle qui permet, si on le souhaite, d'apporter des informations supplémentaires au titre (le scénario ci-dessus ne contient pas cette description optionnelle)

Un scénario est ensuite décrit par un ensemble d'étapes.  
Chaque étape est introduite par un mot-clé suivi d'un texte libre qui correspond à sa description.


#### Question  

***D'après ce qui précède et en tenant compte des remarques qui suivent, proposer une première modélisation du diagramme de classes autour du scénario.***
 
#### Remarques 

Dans le scénario précédent, les mots clés des étapes sont **`Given`**, **`When`**, **`Then`**.   
Ces mots-clés permettent de structurer le scénario en 3 parties selon un **pattern AAA** (**A**rrange-**A**ct-**A**ssert) :    
	- la partie **Arrange** (introduite par le mot clé **`Given`**) décrit les **conditions initiales** du scénario, c-a-d le contexte dans lequel va se dérouler le scénario.  
	- la partie **Act** (introduite par le mot clé **`When`**) décrit un événement ou une **action** qui va réellement déclencher le scénario.  
	- la partie **Assert** (introduite par le mot clé **`Then`**) décrit le **comportement attendu**, ce qui devrait se produire lorsque les conditions initiales sont remplies et l'action est effectuée.


Un scénario n'est pas forcément limité à 3 étapes (pas forcément 1 étape par partie). Il pourra être composé d'autant d'étapes que nécessaire pour bien le décrire (voire même d'aucune étape). 
Par exemple :
  
```gherkin  

Scenario: Buy last coffee  
  Given there are 1 coffee left in the machine  
  Given I have deposited 1$  
  When I press the coffee button  
  Then I should be served a coffee  
```


De plus, pour alléger la lecture d'un scénario, le [Gherkin](https://cucumber.io/docs/reference#gherkin) propose deux autres mots clés **`And`** et **`But`**.  
`And` ou `But` reprennent simplement le mot-clé de l'étape précédente.  
Le scénario précédent pourrait donc également s'écrire de la manière suivante :

```gherkin  

Scenario: Buy last coffee  
  Given there are 1 coffees left in the machine  
  And I have deposited 1$  
  When I press the coffee button  
  Then I should be served a coffee  
```


Enfin, il est à noter que pour être utilisé de manière conviviale, le Gherkin doit proposer une ***internationalisation de ses mots-clés*** afin qu'un scénario puisse être écrit dans la langue maternelle de son rédacteur. C'est-à-dire aussi bien **en français...**  

```gherkin   

Scenario:    
  Etant donné que offrir un gâteau rend heureux  
  Lorsqu' on m'offre 1 gâteau  
  Alors je suis heureux  
```

...qu'**en norvégien** (extrait : [https://github.com/cucumber/cucumber/wiki/Spoken-languages](https://github.com/cucumber/cucumber/wiki/Spoken-languages))

```gherkin   

Scenario: to tall  
    Gitt at jeg har tastet inn 5  
    Og at jeg har tastet inn 7  
    Når jeg summerer  
    Så skal resultatet være 12   
``` 

... ou même en emoji (à noter que le scénario suivant ne comprend qu'une seule étape)  


```gherkin   

📕: 💃  
  😐 🎸  

``` 

Par défaut, les mots clés utilisés pour introduire une étape d'un scénario sont **`Given`**, **`When`**, **`Then`**, **`But`** et **`And`**. Pour permettre l'internationalisation (plus de 60 langages), les mots clés pourront prendre d'autres valeurs. Ces valeurs seront stockées en fonction du langage dans un fichier json de type gherkin-languages.json disponible [ici](https://github.com/cucumber/gherkin/blob/master/gherkin-languages.json).  
Pour l'instant, l'internationalisation sera transparente dans notre modélisation, les mots-clés devront simplement être considérés comme des attributs de type chaine qui pourront prendre une certaine valeur.  

## 2. Ecrire un scénario paramétré, c'est possible ? <a id="scenarioParametre"></a>

Considérons les deux scénarii suivants :  

```gherkin  

Scenario: eat 5 out of 12  
  Given there are 12 cucumbers  
  When I eat 5 cucumbers  
  Then I should have 7 cucumbers  

Scenario: eat 5 out of 20  
  Given there are 20 cucumbers  
  When I eat 5 cucumbers  
  Then I should have 15 cucumbers   
```  

La lecture de ces deux scénarii montre une duplication dans le texte des étapes : seules les valeurs numériques changent, donnant ainsi deux exemples différents pour un enchaînement d'étapes identique.

Le langage Gherkin doit permettre de paramétrer de tels scénarii en les regroupant au sein d'un seul et même scénario dit **scénario paramétré** qui devra être introduit à l'aide du mot clé **`Scenario Outline`** et possèdera un **tableau d'exemples** pour faciliter l'écriture/lecture de ce scénario. 

Par exemple, le scénario paramétré suivant permet de regrouper les deux scénarii précédents :  

```gherkin  
  
Scenario Outline: eating  
  Given there are <start> cucumbers  
  When I eat <eat> cucumbers  
  Then I should have <left> cucumbers  

  Examples:  
    | start | eat | left |  
    |  12   |  5  |  7   |  
    |  20   |  5  |  15  |  
```  

Nous constatons que le **scénario paramétré** est introduit par un mot clé qui prend, par défaut, la valeur de **`Scenario Outline`**. (Ce mot clé pourrait très bien prendre la valeur de `Plan de Scénario` dans le cadre d'un scénario entièrement écrit en français, ou la valeur `📖` pour de l'emoji ou encore `AbstracktScenario` pour du norvégien comme indiqué dans le fichier [gherkin-languages.json ](https://github.com/cucumber/gherkin/blob/master/gherkin-languages.json)).    
Le scénario paramétré est rédigé comme un scénario normal avec :  
-	éventuellement un nom (ici `eating`) et une description,    
-	et bien sûr un ensemble d'étapes. Ces étapes sont paramétrées dans le sens où elles contiennent des paramètres notés `<` et `>` dans le texte.    

Ce qui différencie un scénario normal d'un scénario paramétré est l'apparition d'une **nouvelle rubrique** contenant les **exemples** à appliquer dans les étapes paramétrées du scénario.  
   
- Cette rubrique est introduite par un mot-clé : **`Examples`** par défaut (mais qui pourrait aussi très bien être `Exemples` en français, `📓` en émoji ou même `Eksempler` en norvégien...) 
 
- En plus du mot clé (**`Examples`**), cette rubrique peut éventuellement être décrite par un nom et une description (ce n'est pas le cas ici, mais jetez un petit coup d'œil à la remarque suivante...)      

- Cette rubrique est ensuite composée d'un ensemble de lignes :  
 	- La première ligne constitue en quelque sorte l'***entête*** des exemples puisque chaque cellule de cette ligne prend comme valeur le nom d'un paramètre. Cette ligne est donc constituée d'autant de cellules que de paramètres du scénario. Cette ligne est unique et indispensable.  
 	- Toutes les autres lignes représentent réellement un exemple (plus précisément, le ***corps*** de l'exemple)  c-a-d que chaque cellule d'une ligne contient la valeur d'un paramètre à appliquer dans une étape.  
 	Au travers des valeurs qu'elle propose, chaque ligne permet donc de reconstituer un exemple du scénario. Il y aura donc autant de lignes que d'exemples à illustrer.

Cet ensemble de lignes compose, en langage Gherkin, une **DataTable**.  

Remarque : le symbole `|` est le séparateur qui permet de délimiter chaque cellule de la table. Il pourra être considéré comme une ***constante*** **du langage Gherkin**.

#### Question  

***Pour simplifier la modélisation, on considérera que les étapes sont pour le moment uniquement constituée d'un mot-clé et de texte (peu importe pour l'instant les `<` `>` dans le texte qui permettent d'identifier un paramètre).***

***Transformer votre diagramme de classes afin d'y faire apparaître les deux types de scénarii (scénario simple et scénario paramétré) au sein d'une même hiérarchie (héritage)*** 

***Bien veiller à ce que tous les composants du scénario paramétré soient bien modélisés dans votre nouveau diagramme de classes.***


#### Remarques
 
Un même scénario paramétré peut être composé de **plusieurs tables d'exemples** (différentes rubriques introduites par le mot-clé `Examples`) comme le montre les extraits suivants du livre [The Cucumber Book](https://pragprog.com/book/hwcuc/the-cucumber-book).  
En effet, quand il y a de nombreux exemples, découper en plusieurs tables peut faciliter la lecture du scénario.    
Il est alors intéressant de **nommer** les tables d'exemples, notamment si on choisit de les découper selon des exemples correspondants à un cas nominal (`Successfull withdrawal`) et selon des exemples correspondant à un cas d'erreurs (`Successfull to withdraw too much`).  

```gherkin  

Scenario Outline: Withdraw fixed amount   
  Given I have <Balance> in my account  
  When I choose to withdraw the fiwed amount of <Withdrawal>  
  Then I should <Outcome> cucumbers  
  And the balance of my account should be <Remaining>  

  Examples: Successfull withdrawal  
    | Balance | Withdrawal  | Outcome 	       	| Remaining	|  
    |  500€   |   50€  	    | receive 50€ cash  | 450€	    |  
    |  500€   |  100€  	    | receive 100€ cash | 400€	    |  


  Examples: Successfull to withdraw too much  
    | Balance | Withdrawal | Outcome 	       	 | Remaining |  
    |  100€   |  200€  	   | see an error message| 100€	     |  
    |    0€   |   50€  	   | see an error message|   0€	     |  
```  



Il est également intéressant d'ajouter des **descriptions** (`Passwords are invalid if less than 4 characters`, `Passwords need both letters and numbers to be valid`), notamment si les différentes tables d'exemples visent chacune à vérifier une règle métier distincte :  

```gherkin  

Scenario Outline: Password validation   
  Given I try to create an account with password "<Password>"  
  Then I should see that the password is <Valid or Invalid>  

  Examples: Too short  
    Passwords are invalid if less than 4 characters    
    | Password | Valid or Invalid |  
    |  abc     |  invalid	 	  |  
    |  ab1     |  invalid	 	  |  

  Examples: Letters and Numbers
    Passwords need both letters and numbers to be valid  
    | Password 	| Valid or Invalid |  
    |  abc1   	|  valid	 	   |  
    |  abcd   	|  invalid	 	   |  
    |  abcd1   	|  valid	 	   |  

```

## 3. Factoriser des étapes communes à différents scénarii, c'est possible ? <a id="background"></a>

Considérons les deux scénarii suivants (extraits de [The Cucumber Book](https://pragprog.com/book/hwcuc/the-cucumber-book)) :  

```gherkin  

Scenario: Change PIN successfully  
  Given I have been issued a new card  
  And I insert the card, entering the correct PIN  
  When I choose "Change PIN" from the menu  
  And  I change the PIN to 9876  
  Then the system should remember my PIN is now 9876  

Scenario: Try to change PIN to the same as before  
  Given I have been issued a new card  
  And I insert the card, entering the correct PIN  
  When I choose "Change PIN" from the menu  
  And  I try to change the PIN to the original PIN number  
  Then I should see a warning message  
  And the system should not hava changed my PIN
```    

La lecture de ces deux scénarii montre une répétition mot par mot des trois premières étapes. Pour éviter une telle duplication, le langage Gherkin donne la possibilité de factoriser ces étapes dans une rubrique commune appelée **`Background`** qui doit apparaître avant la description d'un (des) scénario(s) (puisque cette rubrique sera exécutée avant chaque scénario).

Pour améliorer leur lisibilité, les deux scénarii précédents peuvent être écrits avec un **`Background`** de la manière suivante :  

```gherkin   

Background:   
  Given I have been issued a new card  
  And I insert the card, entering the correct PIN  
  When I choose "Change PIN" from the menu  

Scenario: Change PIN successfully  
  When  I change the PIN to 9876  
  Then the system should remember my PIN is now 9876  

Scenario: Try to change PIN to the same as before  
  And  I try to change the PIN to the original PIN number  
  When I should see a warning message  
  And the system should not hava changed my PIN  
  
```

Comme le scénario et le scénario paramétré, le ***background*** n'est ni plus ni moins qu'une nouvelle description de scénario composée :  
-	d'un ensemble d'étapes : les étapes communes à tous les autres scénarii.  
-	Et éventuellement un nom et d'une description. Nous aurions très bien pu décrire l'entête du bakground précédent de la manière suivante :

```gherkin   

Background: Insert a newly issued car and sign in  

   Whenever the bank issues new cards to customers, they are supplied with a Persona Identification Numer (PIN) that is randomly generated by the system.  

   Given I have been issued a new card  
   And I insert the card, entering the correct PIN  
   When I choose "Change PIN" from the menu  

``` 

#### Question

***Modéliser le background dans votre diagramme de classes.***


## 4. Des scénarii, mais pourquoi? ... pour décrire une fonctionnalité ! <a id="feature"></a>

Un scénario est un exemple qui vise à tester une règle métier d'une fonctionnalité du système dans une certaine configuration.  
Un ***scénario*** dépend donc d'une et d'une seule fonctionnalité (appelée ***feature*** dans le langage Gherkin) et une ***feature*** peut être illustrée à l'aide de plusieurs scénarii.

La fonctionnalité est décrite dans une rubrique ***feature*** qui doit être ajoutée en amont de la description des scénarii c-a-d en tout début de fichier avant les rubriques de background, scenario ou scenario paramétré.

Les scénarii de l'exemple précédent étaient destinés à illustrer la ***fonctionnalité*** permettant de **modifier le code PIN d'une carte bancaire**. En rajoutant, la rubrique **feature**, cet exemple devient (extrait de [The Cucumber Book](https://pragprog.com/book/hwcuc/the-cucumber-book)) :


```gherkin   

Feature : Change PIN  

   As soon as the bank issues new cards to customers, they are supplied with a Personnel Identification Number (PIN) that is randomly generated by the system
  
   In order to be able to change it something they can easily remeber, customers with new bank cards need to be able to change their PIN using the ATM.

Background: Insert a newly issued car and sign in  

   Whenever the bank issues new cards to customers, they are supplied with a Personal Identification Numer (PIN) that is randomly generated by the system.  
  
  Given I have been issued a new card  
  And I insert the card, entering the correct PIN  
  When I choose "Change PIN" from the menu  


Scenario: Change PIN successfully  
  When  I change the PIN to 9876  
  Then the system should remember my PIN is now 9876  


Scenario: Try to change PIN to the same as before  
  And  I try to change the PIN to the original PIN number  
  When I should see a warning message  
  And the system should not hava changed my PIN  

```

Une rubrique ***feature*** est donc caractérisée :  
-	par un mot-clé : **`Feature`** par défaut (mais qui pourrait tout aussi bien être `Fonctionnalité` en français, `Egenskap` en norvéngien ou `📚` En émoji comme indiqué dans le fichier [gherkin-languages.json ](https://github.com/cucumber/gherkin/blob/master/gherkin-languages.json))      
-	par un nom  
-	par une description  
-	et par un langage puisque c'est dans cette rubrique ***feature*** que sera paramétrée l'éventuelle internationalisation comme le montre l'exemple ci-après qui permet d'écrire les scénarii en norvégien (extrait de [https://github.com/cucumber/cucumber/wiki/Spoken-languages](https://github.com/cucumber/cucumber/wiki/Spoken-languages))  

```gherkin

  # language: no  
  Egenskap: Summering  
    For å unngå at firmaet går konkurs  
    Må regnskapsførerere bruke en regnemaskin for å legge sammen tall  

  Scenario: to tall  
    Gitt at jeg har tastet inn 5  
    Og at jeg har tastet inn 7  
    Når jeg summerer  
    Så skal resultatet være 12  

  Scenario: tre tall  
    Gitt at jeg har tastet inn 5  
    Og at jeg har tastet inn 7  
    Og at jeg har tastet inn 1  
    Når jeg summerer  
    Så skal resultatet være 13  
```

#### Question

***Faites en sorte que votre modélisation prenne désormais en compte la rubrique feature.***



## 5. Et où écrit-on le tout ? ... dans un document Gherkin ! <a id="gherkinDocument"></a>

Comme indiqué sur le wiki de Gherkin [ici](https://github.com/cucumber/cucumber/wiki/Gherkin), un document rédigé en Gherkin doit respecter certaines conventions : 
 
-	il doit être enregistré dans un fichier portant l'extension **`.feature`**   
-	il ne peut contenir la description que d'**une seule fonctionnalité** (***feature***)   

Outre la description de la fonctionnalité (au travers de ses scénarii), un document Gherkin peut contenir des commentaires. Pour simplifier la modélisation, on considérera qu'un commentaire n'est autre que du texte (qui commence en réalité par le caractère `#`).

Remarque : le symbole **`#`** pourra être considéré comme une ***constante*** **du langage Gherkin**.


#### Question

***Faites en sorte que votre modélisation prenne désormais en compte le document Gherkin.***


## 6. Des tags pour finir... <a id="tag"></a>

Comme indiqué dans le manuel de référence de Cucumber [ici](https://cucumber.io/docs/reference#tags):  
> Tags are a way to **group Scenarios**. They are **@-prefixed strings** and you can place as many tags as you like above **Feature**, **Scenario**, **Scenario Outline** or **Examples** keywords. Space character are invalid in tags and may separate them. 

Le Gherkin permet donc, si on le souhaite, d'ajouter des tags à la **feature**, aux **scénarii**, aux **scénarii paramétrés** et aux **exemples**. Chacun de ces éléments peut avoir aucun, 1 ou plusieurs tags. Un tag possède donc un nom (qui doit être préfixé de **`@`**).

Remarque : le symbole **`@`** pourra être considéré comme une ***constante*** **du langage Gherkin**.

Pour illustrer cette notion de **tag**, prenons l'exemple d'une feature du [projet Gitlab](https://about.gitlab.com/), projet open-source hébergé sur Github à l'adresse suivante ([https://github.com/gitlabhq/gitlabhq](https://github.com/gitlabhq/gitlabhq)) et auquel vous pouvez contribuez si le cœur vous en dit (tout est expliqué [ici](https://github.com/gitlabhq/gitlabhq/blob/master/CONTRIBUTING.md)).

[Gitlab](https://github.com/gitlabhq/gitlabhq) est donc un projet open source utilisant [Cucumber](https://cucumber.io/) pour automatiser ses tests fonctionnels.  
Les documents Ghekin de ce projet se trouvent dans le dépôt **`features`** et sont organisés par domaine fonctionnel : jetez un petit coup d'oeil par [ici](https://github.com/gitlabhq/gitlabhq/tree/master/features).

Pour illustrer la notion de **tag**, nous avons choisi le document gherkin [new_projet.feature](https://github.com/gitlabhq/gitlabhq/blob/master/features/dashboard/new_project.feature) de **`dashboard`**.

```gherkin  

@dashboard  
Feature: New Project  

Background:  
  Given I sign in as a user  
  And I own project "Shop"  
  And I visit dashboard page  
  And I click "New project" link  
  
  @javascript  
  Scenario: I should see New Projects page  
  Then I see "New Project" page  
  Then I see all possible import options  
  
  @javascript  
  Scenario: I should see instructions on how to import from Git URL  
  Given I see "New Project" page  
  When I click on "Repo by URL"  
  Then I see instructions on how to import from Git URL  
  
  @javascript  
  Scenario: I should see instructions on how to import from GitHub  
  Given I see "New Project" page  
  When I click on "Import project from GitHub"  
  Then I am redirected to the GitHub import page  
  
  @javascript  
  Scenario: I should see Google Code import page  
  Given I see "New Project" page  
  When I click on "Google Code"  
  Then I redirected to Google Code import page  

```

Les tags permettront par exemple de :  

-	marquer le domaine fonctionnel que le scénario couvre (comme **`@dashboard`**)   
-	filtrer l'exécution de certains scénarii, par exemple ne lancer que les scénarii marqué (**`@javascript`**) ou au contraire ne pas lancer les scénarii qui sont en court de développement et qui sont taggés d'une certaine manière.   


#### Question  

***Faites en sorte que votre modélisation prenne désormais en compte le concept de tag.***

## 7. Les yeux dans le code  : de la conception à l'implémentation Java <a id="yeuxDansCode"></a>

En modélisant le diagramme de classes, la première partie de ce tutoriel nous a permis de travailler autour de la phase de conception du projet Gherkin.  
La seconde partie de ce TD va vous plonger dans la phase d'implémentation.  


Le **projet Gherkin** est un projet open-source hébergé sur github à l'adresse suivante : **[https://github.com/cucumber/cucumber/tree/master/gherkin](https://github.com/cucumber/cucumber/tree/master/gherkin)**.  

Il s'agit d'un parseur et d'un compilateur pour le langage Gherkin.  
Ce projet propose des implémentations autour du langage Gherkin dans de nombreux langages : 
[.NET](https://github.com/cucumber/gherkin-dotnet), [Java](https://github.com/cucumber/gherkin-java), [JavaScript](https://github.com/cucumber/gherkin-javascript), [Ruby](https://github.com/cucumber/gherkin-ruby), [Go](https://github.com/cucumber/gherkin-go), [Python](https://github.com/cucumber/gherkin-python), [Objective-C](https://github.com/cucumber/gherkin-objective-c) et [Perl](https://github.com/cucumber/gherkin-perl).

Dans le [README](https://github.com/cucumber/cucumber/blob/master/gherkin/README.md) de ce projet, vous trouvez comme documentation le diagramme de classes que vous venez de modéliser, diagramme de classes, il faut descendre un peu pour l'apercevoir :simple_smile:

Nous nous intéresserons dans le cadre de ce tutoriel uniquement à l'implémentation [Java](https://github.com/cucumber/gherkin-java) de ce projet.

Rendez-vous dans le dépôt Java : **[https://github.com/cucumber/gherkin-java](https://github.com/cucumber/gherkin-java)**.  
Puis dans [src](https://github.com/cucumber/cucumber/tree/master/gherkin/java/src), puis dans [main](https://github.com/cucumber/cucumber/tree/master/gherkin/java/src/main) :

- dans [resources/gherkin](https://github.com/cucumber/cucumber/tree/master/gherkin/java/src/main/resources/gherkin), vous trouverez le fichier [**`gherkin-languages.json`**](https://github.com/cucumber/cucumber/blob/master/gherkin/java/src/main/resources/gherkin/gherkin-languages.json) utilisé pour l'internationalisation. 
 
- dans [java/io/cucumber/gherkin](https://github.com/cucumber/cucumber/tree/master/gherkin/java/src/main/java/io/cucumber/gherkin) se trouvent les constantes du langage Gherkin dans le fichier **`GherkinLanguageConstants.java`**.  


- dans [java/io/cucumber/gherkin]( https://github.com/cucumber/cucumber/tree/master/gherkin/java/src/main/java/io/cucumber/gherkin) se trouve également le code du fichier [**`GherkinDocumentBuilder.java`**](https://github.com/cucumber/cucumber/blob/master/gherkin/java/src/main/java/io/cucumber/gherkin/GherkinDocumentBuilder.java) qui indique que les classes qui viennent d'être modéliser se trouvent désormais  dans le package **`io.cucumber.messages.Messages.GherkinDocument.Feature`**. Le code de ces classes est donc disponible dans le répertoire **`gherkin-messagges`** du dépôt [**`cucumber-jvm`**](https://github.com/cucumber/cucumber-jvm), c-a-d plus précisément ici : https://github.com/cucumber/cucumber-jvm/tree/main/gherkin-messages/src/main/java/io/cucumber/core/gherkin/messages   



Consultez le contenu de ce dépôt([cucumber/cucumber-jvm/gherkhin-messages](https://github.com/cucumber/cucumber-jvm/tree/main/gherkin-messages).  
Dans le [src/main/java/io/cucumber/core/gherkin/messages](https://github.com/cucumber/cucumber-jvm/tree/main/gherkin-messages/src/main/java/io/cucumber/core/gherkin/messages), vous devriez y trouver une implémentation Java des classes découvertes lors de la modélisation du diagramme de classes à quelques exceptions près :

* Le langage Gherkin a été implémenté sous forme d'[arbre syntaxique abstrait](https://fr.wikipedia.org/wiki/Arbre_syntaxique_abstrait) (**a**bstract **s**yntax **t**ree en anglais ou **ast**). Nous ne rentrerons pas dans ce tutoriel, dans le détail de ce qu'est un [arbre syntaxique abstrait](https://fr.wikipedia.org/wiki/Arbre_syntaxique_abstrait). Nous nous contenterons juste de retenir le point suivant : **le langage Gherkin a été implémenté sous forme d'arbre et un arbre est composé de nœuds**, d'où :
	- la présence de la classe [Node](https://github.com/cucumber/cucumber/blob/master/gherkin/java/src/main/java/gherkin/ast/Node.java) (que nous n'avions pas identifié dans le diagramme de classes).
	- Tous les éléments du langage Gherkin sont des noeuds, autrement dit les classes héritent de la classe [Node](https://github.com/cucumber/cucumber/blob/master/gherkin/java/src/main/java/gherkin/ast/Node.java).

* La présence d'une classe [DocString](https://github.com/cucumber/cucumber/blob/master/gherkin/java/src/main/java/gherkin/ast/DocString.java).   
Le [manuel de référence à propos de Doctring](https://cucumber.io/docs/reference#doc-strings) indique :
> Doc Strings are handy for passing a larger piece of text to a step definition. The text should be offset by delimiters consisting of three double-quote marks on lines of their own

et donne comme exemple :

```gherkin 
  
Given a blog post named "Random" with Markdown body  
  """  
  Some Title, Eh?  
  ===============  
  Here is the first paragraph of my blog post. Lorem ipsum dolor sit amet,  
  consectetur adipiscing elit.  
  """  

```

On retrouve d'ailleurs la constante permettant d'introduire un [DocString](https://github.com/cucumber/cucumber/blob/master/gherkin/java/src/main/java/gherkin/ast/DocString.java) à savoir **`"""`** déclaré comme : `String DOCSTRING_ALTERNATIVE_SEPARATOR = "```" ;``   dans la classe [`GherkinLanguageConstants.java`](https://github.com/cucumber/cucumber/tree/master/gherkin/java/src/main/java/io/cucumber/gherkin) 



#### Question  

L'objet de cette partie est de se balader dans le code d'implémentation du Gherkin...


**Et pour finir**, n'hésitez consulter les liens suivants :

* [https://github.com/cucumber/cucumber/tree/master/gherkin/testdata/good](https://github.com/cucumber/cucumber/tree/master/gherkin/testdata/good)
* [https://github.com/cucumber/cucumber/tree/master/gherkin/testdata/bad](https://github.com/cucumber/cucumber/tree/master/gherkin/testdata/bad)


Ils vous donneront au travers d'exemples sur les éléments du langage Gherkin quelques conseils pour bien écrire vos scénarii, puisque ce sont des scénarii en Gherkin écrits pour tester l'implémentation du langage Gherkin...Amusant, non ? 


#### Remarque

Vous pouvez bien sûr contribuer au [projet Gherkin](https://github.com/cucumber/cucumber), et on ne peut que vous encourager à aller vers ce genre d'initiative en tant que (futur) développeur.  
  
Si tel est votre souhait, il vous faudra alors cloner le projet (et pas simplement récupérer le zip). Mais tout est expliqué dans le **CONTRIBUTING.md** disponible [ici](https://github.com/cucumber/cucumber/blob/master/CONTRIBUTING.md).



## Pour aller plus loin...

Un tutoriel de prise en main de [Cucumber](https://cucumber.io/) est disponible [ici](https://github.com/iblasquez/tuto_bdd_cucumber)
 


## Commentaires, remarques et suggestions
Pour les discussions, c'est par là : https://github.com/iblasquez/tuto_bdd_gherkin/issues  
Pour les propositions de contenu, de modification par ici : https://github.com/iblasquez/tuto_bdd_gherkin/pulls

Et bien sûr, n'hésitez pas à personnaliser vos messages avec des [emojis](http://www.webpagefx.com/tools/emoji-cheat-sheet/) :simple_smile:


## Licence


Tous les documents de ce dépôt sont placés sous licence CC BY-NC-SA :  [Creative Commons
Attribution - Pas d'Utilisation Commerciale - Partage dans les Mêmes Conditions](https://creativecommons.org/licenses/by-nc-sa/4.0/)

<img src="https://licensebuttons.net/l/by-nc-sa/3.0/88x31.png" width="100">

En savoir plus sur [les licences Creative Commons](https://creativecommons.org/licenses/?lang=fr-FR) ...

Toutefois, toute personne enseignant ou ayant enseignée au département Informatique de l'IUT du Limousin doit demander une autorisation préalable par écrit si elle souhaite utiliser ce document.