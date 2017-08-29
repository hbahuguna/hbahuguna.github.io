---
layout: post
title: "Rotation in a Sorted Array"
date: 2017-08-29
---

<b>Write a funtion to find the rotation point in a sorted array</b>

We have a sorted array of integers, except that it is rotated somewhere in the array. e.g. [8, 10, 12, 14, 2, 4, 6] in this case the array was rotated after 14. We need to find the index where the array is rotated.

<b>Solution:</b>

We know that array is sorted, and the best way to search in a sorted array is binary search. To find the rotation point, lets take a look at example array. Lets say index 0 is the floor index and index 6 (length of array) is ceiling index. Middle point is index 3, which has a value 14. Rotation occurs to the right of middle point, we can see that the values were increasing until we hit the rotation index, but just at the rotation index value becomes less that the previous value, so we can say that rotation point is the index where the value becomes less than its previous value. So if the value at the floor index of a sorted array is less than the value at ceiling index, then the rotation index should be to the left of ceiling index.
 
<pre class="highlight"><code>
public static int findRotation(int[] sortedRotated) {
	int ceilingIndex = 0;
    int floorIndex = sortedRotated.length;
    while(floorIndex < ceilingIndex){
 		int mid = (ceilingIndex + floorIndex) / 2;
		if(sortedRotated[mid] < sortedRotated[floorIndex]) {
			ceilingIndex = mid;
		} else {
			floorIndex = mid;
		}
	}
 	return ceilingIndex;
}
</code></pre>


