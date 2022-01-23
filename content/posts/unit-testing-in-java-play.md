---
title: "Unit Testing in Java Play"
date: 2016-04-05T00:37:56-08:00
draft: false
aliases:
    - /unit-testing-in-java-play/
---

The [Java Play framework](https://www.playframework.com/) is a Java development framework built on top of [Akka](http://akka.io/) that makes it easy to write scalable web services in either Java or Scala. It is fully RESTful and comes with a number of great features such as hot reloading, type safety and in-browser errors. If you are a Java developer who hasn’t yet tried Play, you are missing out on one of the best web frameworks ever developed for the language.

![Java Play Logo](/images/unit-testing-in-java-play/play_full_color.png)

The advent of Play Version 2.4 brought with it some significant architectural changes. Most notably, version 2.4 introduced dependency injection via Guice as the new default architecture for managing dependencies between Play classes. Dependency injection is great in that it allows developers to write less tightly-coupled code. From [Wikipedia](https://en.wikipedia.org/wiki/Dependency_injection):


>This means the client code does not need to know about the injecting code. The client does not need to know how to construct the services. The client does not need to know which actual services it is using. The client only needs to know about the intrinsic interfaces of the services because these define how the client may use the services. This separates the responsibilities of use and construction.

Play depends on Google’s [Guice library](https://github.com/google/guice) for resolving dependency injection. If you want one class of your Play code to depend on another, you simply annotate the classes constructor, pass the dependency you want to inject in as an argument, and Guice takes care of the rest. For example:

```java
public class DataSaver {

    private ApiClient apiClient;

    @Inject
    public DataSaver(ApiClient apiClient) {
        this.apiClient = apiClient;
    }

    public int getAndSaveData() {
        ArrayList<String> data = apiClient.getData();
        // Do data saving operation here
        int itemsSaved = saveData(data);
        return itemsSaved;
    }
}
```

In this example, the `ApiClient` is passed as a parameter to the DataSaver class, which means that we can pass in anything to serve as the ApiClient instance. No more tightly coupled dependencies! Let’s see an example of how this makes unit testing easier. We’re using [JUnit](http://junit.org/junit4/) for test assertions and [Mockito](http://mockito.org/) for mocks / stubs:

```java
public class DataSaverTest {
    @Mock
    private ApiClient mockApiClient;

    private DataSaver dataSaver;

    /**
     * The setup block creates a mock instance of ApiClient
     * that we subsequently inject into DataSaver
     */
    @Before
    public void setup() {
        // Mock the ApiClient so that we're
        // not actually querying the API
        mockApiClient = mock(ApiClient.class);

        // Make up some fake data
        ArrayList<String> fakeData = new ArrayList<>();
        fakeData.add("point 1");
        fakeData.add("point 2");

        // Use Mockito to stub out method responses
        when(mockApiClient.getData()).thenReturn(fakeData);

        // Set up test module that injects mocks
        // instead of actual classes
        dataSaver = new DataSaver(mockApiClient);
    }

    /**
     * Once the mock version of ApiClient has been injected,
     * we can call the .getAndSaveData() method without having
     * to worry about our unit test interacting with the actual API
     */
    @Test
    public void canSaveData() {
        int expectedResult = 2;
        int actualResult = dataSaver.getAndSaveData();
        assertEquals(actualResult, expectedResult);
    }
}
```

Using dependency injection makes mocking out the `ApiClient` instance so easy. No more fighting with integration tests just to make your dependencies work. Simply create the mock instance of the class as on line 15, mock whatever method results you need to and feed in the mock as on line 27. Writing tests has never been easier. Now get out there and test your code!
