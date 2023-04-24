# Lab Report: Week 2

## Part 1: String Server

### Code
```java
import java.io.IOException;
import java.net.URI;

class StringHandler implements URLHandler
{
    private String string = "";

    @Override
    public String handleRequest(URI url)
    {
        String[] params = url.getQuery() != null ? url.getQuery().split("=") : new String[0];
        String[] rawPath = url.getPath().split("/");
        int pathCount = 0;
        for (String p : rawPath)
        {
            if (!p.isEmpty())
                pathCount++;
        }

        String[] path = new String[pathCount];
        pathCount = 0;
        for (String p : rawPath)
        {
            if (!p.isEmpty())
            {
                path[pathCount] = p;
                pathCount++;
            }
        }

        if (path.length == 0)
            return "nothing here: root";

        if (path[0].equals("add-message"))
        {
            if (params.length == 0)
                return "couldn't parse params: no params";

            if (params.length > 2)
                return "couldn't parse params: too many params";
            
            if (params[0].equals("s"))
            {
                string += params[1] + "\n";
                return string;
            }

            return "unrecognized param: " + params[0];
        }
        
        return "nothing here: unrecognized path";
    }
}

public class StringServer
{
    public static void main(String[] args) throws IOException
    {
        if(args.length == 0)
        {
            System.out.println("Missing port number! Try any number between 1024 to 49151");
            return;
        }

        int port = Integer.parseInt(args[0]);

        Server.start(port, new StringHandler());
    }
}
```

### Screenshots
![image](hello.png)

*URL* `localhost:4000/add-message?s=Hello`

The method `StringHandler.handleRequest(URI url)` is called with the argument being a URI object representing `localhost:4000/add-message?s=Hello`. The method parses this URI and updates the `StringHandler`'s `String string` field by adding the `Hello` string, which was the argument in the URI.

![image](hello+.png)

*URL* `localhost:4000/add-message?s=How are you`

The method `StringHandler.handleRequest(URI url)` is called with the argument being a URI object representing `localhost:4000/add-message?s=How are you`. The method parses this URI and updates the `StringHandler`'s `String string` field by adding on the `How are you` string, which was the argument in the URI (which is not the same as the argument to the Java method).

## Part 2: Bug Test

The chosen bug is in the `ArrayExamples.averageWithoutLowest(double[])` method.

### Failing input
**Test**
```java
@Test
public void testAverageWithoutLowest()
{
    double[] input0 = { 1.0, 1.0, 1.0, };
    assertEquals(1.0, ArrayExamples.averageWithoutLowest(input0), 0.001);
}
```

**Output**
```
JUnit version 4.13.2
.E
Time: 0.005
There was 1 failure:
1) testAverageWithoutLowest(ArrayTests)
java.lang.AssertionError: expected:<1.0> but was:<0.0>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:555)
        at org.junit.Assert.assertEquals(Assert.java:685)
        at ArrayTests.testAverageWithoutLowest(ArrayTests.java:32)

FAILURES!!!
Tests run: 1,  Failures: 1
```

### Passing input
**Test**
```java
@Test
public void testAverageWithoutLowest()
{
    double[] input1 = { 1.0, 2.0, 4.0, };
    assertEquals(3.0, ArrayExamples.averageWithoutLowest(input1), 0.001);
}
```

**Output**
```
JUnit version 4.13.2
.
Time: 0.005

OK (1 test)
```

### Bug fix
**Before**
```java
static double averageWithoutLowest(double[] arr)
{
    if (arr.length < 2) {
        return 0.0;
    }
    double lowest = arr[0];
    for (double num : arr) {
        if (num < lowest) {
            lowest = num;
        }
    }
    double sum = 0;
    for (double num : arr) {
        if (num != lowest) {
            sum += num;
        }
    }
    return sum / (arr.length - 1);
}
```

**After**
```java
static double averageWithoutLowest(double[] arr)
{
    if (arr.length < 2)
        return 0.0;

    int lowestIdx = 0;
    for (int i = 0; i < arr.length; i++)
    {
        if (arr[i] < arr[lowestIdx])
            lowestIdx = i;
    }

    double sum = 0;
    for (int i = 0; i < arr.length; i++)
    {
        if (i != lowestIdx)
            sum += arr[i];
    }

    return sum / (arr.length - 1);
}
```
The issue is that the original code  checks if an element is equal to the lowest value, so if there are more than one element with the lowest value, all of them are removed,which is why the first test fails, while the second test passes. The fix is to  keep track of the specific index at which the lowest value was found so that the only the element with that specific index is excluded.
## Part 3: Something Learned
I learned how to set up and use the JUnit framework to test code. I have an okay experience with Java, I've never had to set up a unit testing framework for Java before. So this new knowledge of JUnit is greatly beneficial, since testing code is an extremely crucial skill.





