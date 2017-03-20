[RuleBook-Spring Maven Central]:http://search.maven.org/#artifactdetails|com.deliveredtechnologies|rulebook-spring|0.3.4|
[RuleBook-Core Maven Central]:http://search.maven.org/#artifactdetails|com.deliveredtechnologies|rulebook-core|0.3.4|
[Apache 2.0 License]:https://opensource.org/licenses/Apache-2.0

# RuleBook <img src="https://github.com/Clayton7510/RuleBook/blob/master/LambdaBook.png" height="100" align="left"/>
**&raquo; A Simple &amp; Intuitive Rules Abstraction for Java** <br/><sub> _100% Java_ &middot; _Lambda Enabled_ &middot; _Simple, Intuitive DSL_ &middot; _Lightweight_ </sub>

---

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)][Apache 2.0 License] [![Maven Central](https://img.shields.io/badge/maven%20central-0.3.4-brightgreen.svg)][RuleBook-Core Maven Central] [![Build Status](https://travis-ci.org/Clayton7510/RuleBook.svg?branch=master&maxAge=600)](https://travis-ci.org/Clayton7510/RuleBook) [![Coverage Status](https://coveralls.io/repos/github/Clayton7510/RuleBook/badge.svg?branch=develop)](https://coveralls.io/github/Clayton7510/RuleBook?branch=develop)  [![Gitter](https://badges.gitter.im/RuleBook.svg)](https://gitter.im/RuleBook?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

## Why RuleBook?
RuleBook rules are built in the way that Java developers think: Java code. And they are executed in the way that programmers expect: In order. RuleBook also allows you to specify rules using an easy to use Lambda enabled Domain Specific Language or using POJOs that you define!

Tired of classes filled with if/then/else statements? Need a nice abstraction that allows rules to be easily specified in way that decouples them from each other? Want to write rules the same way that you write the rest of your code [in Java]? RuleBook just might be the rules abstraction you've been waiting for!

#### Contents

* [1 Getting RuleBook](#1-getting-rulebook)
  * [1.1 Building RuleBook](#11-building-rulebook)
  * [1.2 Maven Central Releases](#12-maven-central-releases)
  * [1.3 Latest SNAPSHOT Releases](#13-latest-sonatype-snapshot-development-release)
  * [1.4 Adding RuleBook to Your Maven Project](#14-adding-rulebook-to-your-maven-project)
  * [1.5 Adding RuleBook to Your Gradle Project](#15-adding-rulebook-to-your-gradle-project)
* [2 Using RuleBook](#2-using-rulebook)
  * [2.1 A Hello World Example](#21-a-helloworld-example)
  * [2.2 An Example Using Facts](#22-the-above-example-using-facts)
  * [2.3 A \[Slightly\] More Complex Scenario](#23-a-slightly-more-complex-scenario)
* [3 The RuleBook Domain Specific Language](#3-the-java-domain-specific-language-explained)
  * [3.1 Given-When-Then: The Basis of the RuleBook Language](#31-given-when-then-the-basis-of-the-rulebook-language)
  * [3.2 The Using Method](#32-the-using-method)
  * [3.3 The Stop Method](#33-the-stop-method)
  * [3.4 Working With Facts](#34-working-with-facts)
* [4 POJO Rules](#3-pojo-rules)
  * [4.1 A POJO Rules Example](#31-a-hello-world-example)
  * [4.2 A \[Slightly\] More Complex POJO Rules Example](#32-the-megabank-example-with-pojo-rules)
* [5 Using RuleBook with Spring](#4-using-rulebook-with-spring)
  * [5.1 Creating a Spring Enabled POJO Rule](#41-creating-a-spring-enabled-pojo-rule)
  * [5.2 Configuring a RuleBook in Spring](#42-configuring-a-rulebook-in-spring)
  * [5.3 Using a Spring Enabled RuleBook](#43-using-a-spring-enabled-rulebook)
* [6 How to Contribute](#5-how-to-contribute)
  * [6.1 Developer Guidelines](#51-developer-guidelines)
  
## 1 Getting RuleBook

### 1.1 Building RuleBook

```bash
git clone https://github.com/Clayton7510/RuleBook.git
cd RuleBook
./gradlew build
```

### 1.2 Maven Central Releases

* rulebook-core &nbsp;&nbsp;&nbsp;[![Maven Central](https://img.shields.io/badge/maven%20central-0.3.4-brightgreen.svg)][RuleBook-Core Maven Central]
* rulebook-spring [![Maven Central](https://img.shields.io/badge/maven%20central-0.3.4-brightgreen.svg)][RuleBook-Spring Maven Central]

### 1.3 Latest Sonatype SNAPSHOT (Development) Release

* rulebook-core &nbsp;&nbsp;&nbsp;[![Sonatype Nexus](https://img.shields.io/badge/SNAPSHOT-0.3.5-green.svg)](https://oss.sonatype.org/content/repositories/snapshots/)
* rulebook-spring [![Sonatype Nexus](https://img.shields.io/badge/SNAPSHOT-0.3.5-green.svg)](https://oss.sonatype.org/content/repositories/snapshots/)

### 1.4 Adding RuleBook to Your Maven Project

_Add the code below to your pom.xml_

```xml
<dependency>
    <groupId>com.deliveredtechnologies</groupId>
    <artifactId>rulebook-core</artifactId>
    <version>0.3.4</version>
</dependency>
```

### 1.5 Adding RuleBook to Your Gradle Project

_Add the code below to your build.gradle_

```groovy
compile 'com.deliveredtechnologies:rulebook-core:0.3.4'
```

<sub>[Top](#contents)</sub>

## 2 Using RuleBook
### 2.1 A HelloWorld Example
```java
public class ExampleRuleBook extends RuleBook {
  @Override
  public void defineRules() {
    //one rule prints "Hello World"
    addRule(StandardRule.create()
      .then(f -> System.out.print("Hello "))
      .then(f -> System.out.println("World")));
  }
}
```
**...or use 2 rules**
```java
public class ExampleRuleBook extends RuleBook {
  @Override
  public void defineRules() {
    //first rule prints "Hello"
    addRule(StandardRule.create().then(f -> System.out.print("Hello ")));
    //second rule prints "World"
    addRule(StandardRule.create().then(f -> System.out.println("World")));
  }
}
```
**now, run it!**
```java
public class ExampleMainClass {
  public static void main(String[] args) {
    RuleBook exampleRuleBook = new ExampleRuleBook();
    exampleRuleBook.run();
  }
}
```
### 2.2 The Above Example Using Facts
```java
public class ExampleRuleBook extends RuleBook<String> {
  @Override
  public void defineRules() {
    //first rule prints "Hello" value from helloFact
    addRule(StandardRule.create().when(f -> f.containsKey("hello"))
      .using("hello")
      .then(System.out::print));
    //second rule prints "World" value from worldFact
    addRule(StandardRule.create().when(f -> f.containsKey("world"))
      .using("world")
      .then(System.out::println));
  }
}
```
**..or it could be a single rule**
```java
public class ExampleRuleBook extends RuleBook<String> {
  @Override
  public void defineRules() {
    //first rule prints "Hello" value from helloFact
    addRule(StandardRule.create().when(f -> f.containsKey("hello") && f.containsKey)
      .using("hello").then(System.out::print)
      .using("world").then(System.out::println));
  }
}
```
**now, run it!**
```java
public class ExampleMainClass {
  public static void main(String[] args) {
    RuleBook exampleRuleBook = new ExampleRuleBook();
    exampleRuleBook
      .given("hello", "Hello")
      .given("world", "World")
      .run();
  }
}
```
### 2.3 A [Slightly] More Complex Scenario

_MegaBank issues home loans. Each home loan can have up to 3 applicants. If any of the applicant's credit scores is less than 700 then all of the applicants' available cash on hand must be at least $50,000.00, otherwise the loan is denied._

This type of problem lends itself well to Decisions. As stated above, Decsisions accept one type of Fact and return a different type of Result. In this case, the Facts are applicant information for each applicant and the Result is whether the loan is approved or denied. The following code example shows how the rules for this scenario can be implemeted.

```java
public class ApplicantBean {
  private int creditScore;
  private BigDecimal cashOnHand;

  public ApplicantBean(int creditScore, BigDecimal cashOnHand) {
    this.creditScore = creditScore;
    this.cashOnHand = cashOnHand;
  }

  public int getCreditScore() {
    return creditScore;
  }

  public void setCreditScore(int creditScore) {
    this.creditScore = creditScore;
  }

  public BigDecimal getCashOnHand() {
    return cashOnHand;
  }

  public void setCashOnHand(BigDecimal cashOnHand) {
    this.cashOnHand = cashOnHand;
  }
}
```
```java
public class HomeLoanDecisionBook extends DecisionBook<ApplicantBean, Boolean> {
  @Override
  protected void defineRules() {
    //if there are more than 3 applicants then the loan is denied
    addRule(StandardRule.create(ApplicantBean.class).when(factMap -> factMap.size() > 3).stop());

    //if everyone has a credit score of 700 or more then the loan is approved
    addRule(StandardDecision.create(ApplicantBean.class, Boolean.class)
      .when(factMap -> factMap.values().stream()
        .allMatch(applicantFact -> applicantFact.getValue().getCreditScore() >= 700))
      .then(f -> result.setValue(true)));

    //if everyone has cash on hand of greater than or equal to $50,000 then the loan is approved
    addRule(StandardDecision.create(ApplicantBean.class, Boolean.class)
      .when(factMap -> factMap.values().stream()
        .allMatch(applicantFact -> applicantFact.getValue().getCashOnHand().compareTo(BigDecimal.valueOf(50000)) >= 0))
      .then(f -> result.setValue(true)));
  }
}
```
```java
public class ExampleSolution {
  public static void main(String[] args) {
    HomeLoanDecisionBook decisionBook = new HomeLoanDecisionBook();
    decisionBook.withDefaultResult(false)
      .given("applicant1", new ApplicantBean(699, BigDecimal.valueOf(199))
      .given("applicant2", new ApplicantBean(701, BigDecimal.valueOf(51000))
      .run();

    System.out.println(decisionBook.getResult() ? "Loan Approved!" : "Loan Denied!");
  }
}
```
In the above example, the default Result value was initialized to false. So, unless a Decision set the result to something else, the result of running the DecisionBook would be false. And unfortunately, for these applicants, they just didn't meet the requirements for a loan at MegaBank as determined by the rules.

<sub>[[Top](#contents)]</sub>

## 3 The Java Domain Specific Language Explained

The RuleBook Java Domain Specific Language (DSL) is Given-When-Then, popularized by Behavior Driven Development (BDD) and associated testing frameworks (e.g. Cucumber and Spock). And many of the ideas that went into creating the RuleBook Java DSL are borrowed from BDD, including: _**Sentences should be used to describe rules**_ and _**Rules should be defined using a ubiquitous language that translates into the codebase**_.

### 3.1 Given-When-Then: The Basis of the RuleBook Language 

Much like the Given-When-Then language for defining tests that was popularized by BDD, RuleBook uses a Given-When-Then language for defining rules. The RuleBook Given-When-Then methods have the following meanings.

* **Given** some Fact(s)
* **When** a condition evaluates to true
* **Then** a action is triggered

**Given** methods can accept one or more Facts in various different forms and are used as a collection of information provided to a single Rule/Decision or a chain of Rules/Decisions called a RuleBook/DecisionBook.

**When** methods accept a Predicate that evaluates a condition based on the Facts provided. Only one when() method can be specified per Rule/Decision.

**Then** methods accept a Consumer (or BiConsumer for Decisions) that describe the action to be invoked if the condition in the when() method evaluates to true. There can be multiple then() methods specified in a Rule or Decision that will all be 
invoked in the order specified if the when() condition evaluates to true.

### 3.2 The Using Method
**Using** methods reduce the set of Facts available to a then() method. Mutiple using methods can also be chained together if so desired. The aggregate of the Facts with the names specified in all using() methods immediately preceeding a then() method will be made available to that then() method. An example of how using() works [is shown above](#22-the-above-example-using-facts).

### 3.3 The Stop Method
**Stop** methods break the rule chain. If a stop method is specified when defining a rule, it means that if the when() condition evaluates to true, following the completion of the then() action(s), the rule chain should be broken and no more rules in that chain should be evaluated.

### 3.4 Working With Facts
As stated above, Facts are provided to Rules and Decisions using the given() method. The Facts available to Rules/Decisions and RuleBooks/DecisionBooks are contained in a FactMap, which is a special kind of Map that allows for easy access to the underlying objects contained in Facts. The reason why Facts exist is so that there is always a reference to the objects that Rules and Decisions work with - even if say, an immutable object is replaced, the perception is that the Fact still exists and provides a named reference to representative object.

#### 3.4.1 The Single Fact Convenience Method
Facts really only have a single convenience method. Since the FactMap is what is passed into then when() and then() methods, most of the convenience methods around Facts are made available in the FactMap. However, there is one... the constructor. Facts consist of a name value pair. But in some cases, the name of the Fact should just be the string value of the object it contains. In those cases, a constructor with a single argument of the type of the object contained in the Fact can be used.

#### 3.4.2 The FactMap Convenience Methods
Although the reason for FactMaps is important, that doesn't mean anyone wants to chain a bunch of boiler plate calls to get to the value object contained in an underlying Fact. So, some convenience methods are there to make life easier when working with when() and then() methods.

**getOne()** gets the value of the Fact when only one Fact exists in the FactMap

**getValue(String name)** gets the value of the Fact by the name of the Fact

**setValue(String name, T value)** sets the Fact with the name specified to the new value 

**put(Fact fact)** adds a Fact to the FactMap, using the Fact's name as the key for the Map

**toString()** toString gets the toString() method of the Fact's value when only one Fact exists

<sub>[Top](#contents)</sub>

## 4 POJO Rules

As of RuleBook v0.2, POJO rules are supported. Simply define your rules as annotated POJOs in a package and then use _RuleBookRunner_ to scan the package for rules and create a RuleBook out of them. It's that simple!

### 4.1 A Hello World Example

```java
package com.example.pojorules;

import com.deliveredtechnologies.rulebook.annotations.*;
import com.deliveredtechnologies.rulebook.RuleState;

@Rule
public class HelloWorld {

  @Given("hello")
  private String hello;
  
  @Given("world")
  private String world;
  
  @Result
  private String helloworld;
  
  @When
  public boolean when() {
    return true;
  }
  
  @Then
  public RuleState then() {
    helloworld = hello + " " + world;
    return RuleState.BREAK;
  }
}
```
```java

public static void main(String args[]) {
  RuleBookRunner ruleBook = new RuleBookRunner("com.example.pojorules");
  ruleBook.given(new Fact("hello", "Hello"), new Fact("world", "World")).run();
  System.out.println(ruleBook.getResult()); //prints "Hello World"
}
```

### 4.2 The MegaBank Example With POJO Rules

```java
@Rule(order = 1) //order specifies the order the rule should execute in; if not specified, any order may be used
public class ApplicantNumberRule {
  @Given
  private List<ApplicantBean> applicants; //Annotated Lists get injected with all Facts of the declared generic type

  @When
  public boolean when() {
    return applicants.size() > 3;
  }

  @Then
  public RuleState then() {
    return RuleState.BREAK;
  }
}
```
```java
@Rule(order = 2)
public class CreditScoreRule {
  @Given
  private List<ApplicantBean> applicants;

  @Result
  private boolean approved;
    
  @When
  public boolean when() {
    return applicants.stream()
      .allMatch(applicant -> applicant.getCreditScore() >= 700);
  }

  @Then
  public RuleState then() {
    approved = true;
    return RuleState.NEXT;
  }
}
```
```java
@Rule(order = 3)
public class CashOnHandRule {
  @Given
  List<ApplicantBean> applicants; 

  @Result
  private boolean approved;

  @When
  public boolean when() {
    return applicants.stream()
      .allMatch(applicant -> applicant.getCashOnHand().compareTo(BigDecimal.valueOf(50000)) >= 0);
  }

  @Then
  public RuleState then() {
    approved = true;
    return RuleState.BREAK;
  }
}
```
```java
public static void main(String[] args) {
  RuleBookRunner ruleBook = new RuleBookRunner("com.example.rulebook");
  ruleBook.withDefaultResult(false)
    .given(
      new Fact("applicant1", new ApplicantBean(699, BigDecimal.valueOf(199))),
      new Fact("applicant2", new ApplicantBean(701, BigDecimal.valueOf(51000))))
    .run();
  boolean approval = (boolean)ruleBook.getResult();
  System.out.println("Application is " + (approval ? "approved!" : "not approved!"));
}
```

<sub>[[Top](#contents)]</sub>

## 5 Using RuleBook with Spring

RuleBooks in Spring can be created using Spring configurations with RuleBookBean classes. RuleBookBean classes should be scoped as prototype and they can add either rules created through the RuleBook DSL or Spring enabled POJO rules. And creating a Spring enabled POJO rule couldn't be easier; just create a POJO rule, but instead of using @Rule, use @RuleBean.

### 5.1 Creating a Spring Enabled POJO Rule

```java
@RuleBean
public class HelloSpringRule {
  @Given("hello")
  private String hello;
  
  @Result
  private String result;
  
  @When
  public boolean when() {
    return hello != null;
  }
  
  @Then
  public RuleState then() {
    result = hello + " ";
    return RuleState.NEXT;
  }
}
```

### 5.2 Configuring a RuleBook in Spring

```java
@Configuration
public class SpringConfig {
  @Autowired
  private ApplicationContext context;
  
  @Bean
  @Scope("prototype")
  public RuleBookBean ruleBookBean() throws InvalidClassException {
    RuleBookBean ruleBookBean = new RuleBookBean();
    ruleBookBean.addRule(context.getBean(HelloSpringRule.class)); //add a Spring enabled POJO rule
    ruleBookBean.addRule(StandardDecision.create()
      .when(factMap -> factMap.containsKey("world"))
      .then((factMap, result) -> {
        result += factMap.getValue("world");
        return RuleState.BREAK;
      });
    return ruleBookBean;
  }
}
```

### 5.3 Using a Spring Enabled RuleBook

```java
  @Autowired
  private ApplicationContext context;
  
  public void someMethod() {
    RuleBookBean ruleBook = context.getBean(RuleBookBean.class));
    ruleBook.given(new Fact("hello", "Hello"), new Fact("world", "World")).run(); 
    System.out.println(ruleBook.getResult()); //prints "Hello World"
  }
```

<sub>[[Top](#contents)]</sub>

## 6 How to Contribute

Suggestions and code contributions are welcome! Please see the _Developer Guidelines_ below.

### 6.1 Developer Guidelines

Contributions must adhere to the following criteria:

1. The forked repository must be publicly visible.
2. The issues addressed in the request must be associated to an accepted issue.
3. The build (i.e. ./gradlew build) must pass with no errors or warnings.
4. All new and existing tests must pass.
5. The code must adhere to the style guidleines icluded in the checkstyle configuration (i.e. no checkstyle errors).
6. Newly introduced code must have at least 85% test coverage.
7. Pull requests must be for the _develop_ branch.
8. The version number in gradle.properties should match the milestone of the issue its associated with appended with _-SNAPSHOT_ (ex. 0.2-SNAPSHOT)

Anyone may submit an issue, which can be either an enhancement/feature request or a bug to be remediated. If a feature request or a bug is approved (most reasonable ones will be), completed and an associated pull request is submitted that adheres to the above criteria, then the pull request will be merged and the contributor will be added to the list of contributors in the following release.

<sub>[[Top](#contents)]</sub>
