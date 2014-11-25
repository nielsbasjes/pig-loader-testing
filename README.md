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

Update
====
I found the cause!
Somewhere deep down there was call to 'cleanupOnFailure' .. why?
Because of this:

    Unexpected System Error Occured: java.lang.IncompatibleClassChangeError: Found interface org.apache.hadoop.mapreduce.JobContext, but class was expected
    	at org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.PigOutputFormat.setupUdfEnvAndStores(PigOutputFormat.java:243)
    	at org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.PigOutputFormat.checkOutputSpecs(PigOutputFormat.java:190)
    	at org.apache.hadoop.mapreduce.JobSubmitter.checkSpecs(JobSubmitter.java:458)
    	at org.apache.hadoop.mapreduce.JobSubmitter.submitJobInternal(JobSubmitter.java:343)
    	at org.apache.hadoop.mapreduce.Job$10.run(Job.java:1285)
    	at org.apache.hadoop.mapreduce.Job$10.run(Job.java:1282)
    	at java.security.AccessController.doPrivileged(Native Method)
    	at javax.security.auth.Subject.doAs(Subject.java:415)
    	at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1548)
    	at org.apache.hadoop.mapreduce.Job.submit(Job.java:1282)
    	at org.apache.hadoop.mapreduce.lib.jobcontrol.ControlledJob.submit(ControlledJob.java:335)
    	at org.apache.hadoop.mapreduce.lib.jobcontrol.JobControl.run(JobControl.java:240)
    	at org.apache.pig.backend.hadoop20.PigJobControl.run(PigJobControl.java:121)
    	at java.lang.Thread.run(Thread.java:745)
    	at org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher$1.run(MapReduceLauncher.java:279)

Solution:
Change the pig dependency in the pom.xml from this

     <dependency>
       <groupId>org.apache.pig</groupId>
       <artifactId>pig</artifactId>
       <version>0.13.0</version>
     </dependency>

into this

    <dependency>
      <groupId>org.apache.pig</groupId>
      <artifactId>pig</artifactId>
      <version>0.13.0</version>
      <classifier>h2</classifier>
    </dependency>

Spot the difference? The classifier. Pig is still compiled by default against Hadoop 1.x ...