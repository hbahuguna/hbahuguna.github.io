---
layout: post
title: "Highest Product Of Three Numbers in an Array of Integers"
date: 2018-05-12
---

<b>Given an array of integers, find the highest product of three numbers</b>


<b>Solution:</b>

We can possibly find the highest product of three numbers by sorting the array and then multiplying 3 biggest numbers. This approach is O(log(n)) because of sorting, we should try to find out a better solution that solves the problem in O(n) time. 

We would require to iterate over the array at least once. In the process of iterating we can keep track of highestProductOfThree till now by multiplying current number with current highestProductOfTwo. However, there is a case when this might not work just right. When the current number is a negative integer and the one of the products of earlier two numbers was also a high negative number, we could miss a possible highestProductOfThree. So we would need to keep track of highestProductOfTwo and lowestProductOfTwo, and then decide on current highestProductOfThree by multiplying current number with both and compare. In order for us to keep track of highestProductOfTwo and lowestProductOfTwo we also need to keep track of highest and lowest numbers.


<pre class="highlight"><code>
public static int highestProductOfThree(int[] intArray) {
    if(intArray.length < 3) {
        throw IllegalArgumentException("at least 3 numbers required to find highest product of three");
    }

    //find highest and lowest to start with
    int highest = Math.max(intArray[0], intArray[1]);
    int lowest = Math.min(intArray[0], intArray[1]);
    
    //find highestProductOfTwo and lowestProductOfTwo to start with
    int highestProductOfTwo = intArray[0] * intArray[1];
    int lowestProductOfTwo = intArray[0] * intArray[1];

    //initialize highestProductOfThree
    int highestProductOfThree = intArray[0] * intArray[1] * intArray[2];

    for(int i = 2; i < intArray.length; i++) {
        int current = intArray[i];
        // next highest product of three is the higher of current highestProductOfThree
        // and max of multiplication of current and highestProductOfTwo and current and lowestProductOfTwo 
        highestProductOfThree = Math.max(highestProductOfThree, Math.max(
            lowestProductOfTwo * current, highestProductOfTwo * current
        ));

        // next highest product of two is higher of current highestProductOfTwo
        // and max of multiplication of highest and current and lowest and current
        highestProductOfTwo = Math.max(highestProductOfTwo, Math.max(
            highest * current, lowest * current
        ));


        // next lowest product of two is lower of current lowestProductOfTwo
        // and min of multiplication of highest and current and lowest and current
        lowestProductOfTwo = Math.min(lowestProductOfTwo, Math.min(
            highest * current, lowest * current
        ));

        highest = Math.max(highest, current);
        lowest = Math.min(lowest, current);
    }
    return highestProductOfThree;
}
</code></pre>


