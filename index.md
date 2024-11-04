---
layout: default
---

<!-- Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](./another-page.html).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### Header 3 -->

<img width="100%" src="https://github.com/mandalijvm/mandalijvm.github.io/blob/main/log.png?raw=true"/>

**Mandali** is a Java library designed to detect potential thread safety issues within Java classes. 

The name **Mandali** is inspired by the Indonesian words "Man = Aman" (safe) and "Dali = terkenDali" (controlled), symbolizing **safety under control**. 

This library helps identify the use of collections that may not be thread-safe in multi-threaded environments and automatically detects deadlocks when running.

Note: **This is not to guarantee thread safety. It just helps reduce potential thread safety issues**.


## Features

- **Unsafe Collection Detection**: Identifies commonly unsafe collections in multi-threaded contexts, such as `ArrayList`, `HashMap`, and `StringBuilder`.
- **Thread-safe Alternatives Recommendation**: Suggests safe alternatives to replace unsafe collections or objects.
- **Deadlock Detection**: Automatically detects deadlocks in the system and displays detailed information about the locked threads.

## Installation
### In Gradle

```groovy
repositories {
    maven { url 'https://repo.repsy.io/mvn/hangga/repo' }
}

dependencies {
    implementation 'io.mandali:mandali:1.0.6-SNAPSHOT'
}
```
### In Maven
```xml
<repositories>
    <repository>
        <id>mandali-repo</id>
        <url>https://repo.repsy.io/mvn/hangga/repo</url>
    </repository>
</repositories>

<dependencies>
    <dependency>
        <groupId>io.mandali</groupId>
        <artifactId>mandali</artifactId>
        <version>1.0.6-SNAPSHOT</version>
    </dependency>
</dependencies>
```

## Basic Usage
To start the Mandali analysis, simply create an instance of Mandali and call start() on it:
```Kotlin
Mandali(this).start()
```

## Annotation Usage: @RunMandali
This annotation should be applied at the class level of a test to trigger the analysis based on the specified options:
```Kotlin
// Add annotation here
@RunMandali(showDate = true, detectDeadlock = true)
class MandaliExampleUnitTest {
    // Test cases
}
```

| Param          |descriptions| type              | default |
|:---------------|:---------------|:------------------|:--------|
| showDate       | A Boolean parameter that, when set to true, displays the date and time of the analysis run in the output.| boolean | true    |
| detectDeadlock | A Boolean parameter that enables deadlock detection|boolean   | false    |

## Example Test Class
The following example demonstrates how to structure a test class to check for thread safety issues using the Mandali library. This class simulates potential deadlocks and thread-unsafe scenarios, which are detected and analyzed by Mandali.

```Kotlin
package io.mandali

import org.junit.jupiter.api.Test
import java.lang.Thread.sleep
import kotlin.concurrent.thread

class Account(val name: String, var balance: Int) {

    private fun deposit(amount: Int) {
        balance += amount
    }

    private fun withdraw(amount: Int) {
        balance -= amount
    }

    fun transfer(to: Account, amount: Int) {
        println("${this.name} tries to transfer $amount to ${to.name}.")
        synchronized(this) {
            sleep(10) // Simulate processing time
            if (balance >= amount) {
                withdraw(amount)
                synchronized(to) {
                    to.deposit(amount)
                }
            }
        }
    }
}

@RunMandali(showDate = true, detectDeadlock = true)
class MandaliExampleKotlinUnitTest {

    @Test
    fun `example of deadlock`() {
        val account1 = Account("Hangga", 1000)
        val account2 = Account("John", 1000)
        val account3 = Account("Alice", 2000)

        // Transfer from account1 to account2
        thread {
            account1.transfer(account2, 100)
        }.join(10) // as a simulation of the time required

        // Transfer from account2 to account1
        thread {
            account2.transfer(account1, 200)
        }.join(20)

        // Transfer from account3 to account1
        thread {
            account3.transfer(account1, 1000)
        }.join(500)

        Mandali(this).start()
    }

    val list = ArrayList()

    @Test
    fun `example thread-unsafe using HashMap`() {
        val map = HashMap()

        val threads = List(10) { index ->
            thread {
                for (i in 0 until 1000) {
                    map[i] = index
                }
            }
        }

        threads.forEach {
            it.join()
        }
    }
}
```





















<!-- 

#### Header 4

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Small image

![Octocat](https://github.githubassets.com/images/icons/emoji/octocat.png)

### Large image

![Branching](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
``` -->
