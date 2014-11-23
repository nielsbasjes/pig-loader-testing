pig-loader-testing
==================

Trying to test a custom Loader in PIG is somehow hard ...

I was able to reproduce the problem I'm facing by simply trying to run an existing unit test from pig without modification of the code at all.

So what I did:

* Create a very simple pom.xml
* Download https://raw.githubusercontent.com/apache/pig/branch-0.13/test/org/apache/pig/builtin/mock/TestMockStorage.java and put it in the right directory.

Now when you run 

    mvn test

I see:

    java.lang.RuntimeException: No data for location 'bar'
    	at org.apache.pig.builtin.mock.Storage$Data.get(Storage.java:327)
    	at org.apache.pig.builtin.mock.TestMockStorage.testMockStoreAndLoad(TestMockStorage.java:55)

and

    junit.framework.AssertionFailedError: 
    Expected :{a: chararray,b: chararray}
    Actual   :null

and

    java.lang.RuntimeException: No data for location 'output'
        at org.apache.pig.builtin.mock.Storage$Data.get(Storage.java:327)
        at org.apache.pig.builtin.mock.TestMockStorage.testMockStoreUnion(TestMockStorage.java:106)

