---
layout: post
title: "Finding Intersection of Two Rectangles"
date: 2017-08-27
---

<b>Write a funtion to find the rectangular intersection of two given rectangles</b>

Our Rectangle class is as follows :

<pre class="highlight"><code>
public static class Rectangle {
	public static final NO_RECTANGLE = new Rectangle();
	private int leftX;
    private int bottomY;
   	private int width;
  	private int height;
    public Rectangle() {}
    public Rectangel(int leftX, int bottomY, int width, int height) {
		this.leftX = leftX;
		this.bottomY = bottomY;
		this.width = width;
		this.height = height;
  	}
	public int getLeftX() {
		return leftX;
	}
	public int getBottomY() {
		return bottomY;
	}
	public int getWidth() {
		return width;
	}
	public int getHeight() {
		return height;
	}
}
</code></pre>

The output should also be a Rectangle.

<b>Solution:</b>

Let us begin with finding an intersection for two lines on X axis. Then we will extend this to find out an intersection of two lines on Y axis. If there is an intersection on both axis then we can return a new rectangle created by intersection, otherwise we will return NO_RECTANGLE.

To find an intersection on X axis, we need to consider the following cases:
1. Line 1 intersects Line 2.
2. Line 1 completely overlaps Line 2.
3. Line 1 just touches Line 2.
4. Line1 is far apart from Line 2.

First we need to find out what is the condition for Line 1 and Line 2 to intersect. If max of left ends of Line 1 and 2 is less than the min of right ends of Line 1 and 2, then we see that there is an intersection.

<pre class="highlight"><code>
public static class XIntersect {
	public static final NO_INTERSECTION = new XIntersect();
	private int leftX;
	private int width;
    public XIntersect() {}
    public XIntersect(int leftX, int width) {
		this.leftX = leftX;
		this.width = width;
    }
	public int getLeftX() {
		return leftX;
	}
	public int getWidth() {
		return width;
	}
}
</code></pre>

<pre class="highlight"><code>
public static XIntersect findXIntersection(int leftX1, int width1, int leftX2, int width2) {
	int rightX1 = leftX1 + width1;
	int rightX2 = leftX2 + width2;
	int maxLeft = Math.max(leftX1, leftX2);
	int minRight = Math.min(rightX1, rightX2);
	if(maxLeft < minRight) {
		return XIntersect(maxLeft,minRigt - maxLeft);
	} else {
		return XIntersect.NO_INTERSECTION;
	}
}
</code></pre>

We created an XIntersect class that creates an object which represents an intersection. In our findXIntersection method we return an XIntersect object if max of left ends is greater than min of right ends of two lines.

We can use the similar approach to find YIntersection, so we will rather create a common Intersection object that can be used by both X and Y intersections.

<pre class="highlight"><code>
private static class Intersect {
	public static final NO_INTERSECTION = new Intersect();
	private int start;
	private int length;
    public Intersect() {}
    public XIntersect(int start, int length) {
		this.start = start;
		this.length = length;
    }
	public int getStart() {
		return start;
	}
	public int getLength() {
		return length;
	}
}
</code></pre>

<pre class="highlight"><code>
private static Intersect findIntersection(int start1, int length1, int start2, int length2) {
	int end1 = start1 + length1;
	int end2 = start2 + length2;
	int maxStart = Math.max(start1, start2);
	int minEnd = Math.min(end1, end2);
	if(maxLeft < minRight) {
		return XIntersect(maxStart,minEnd - maxStart);
	} else {
		return Intersect.NO_INTERSECTION;
	}
}
</code></pre>

We can use findIntersection method to find intersection on both X and Y axis. We can also say that two rectangles intersect when both X and Y lines of rectangles intersect. We can then return a new Rectangle with width and height of Intersection objects we obtained so far.


<pre class="highlight"><code>
public static Rectangle findRectangleIntersection(Rectangle rect1, Rectangle rect2) {
	Intersect xIntersect = findIntersection(rect1.getLeftX(), rect1.getWidth(), rect2.getLeftX(), rect2.getWidth());
	Intersect yIntersect = findIntersection(rect1.getBottomY(), rect1.getHeigh(), rect2.getBottomY(), rect2.getHeight());
	if(xIntersect.getLength() > 0 && yIntersect.getLength() > 0) {
		return new Rectangle(xIntersect.getStart(), yIntersect.getStart(), xIntersect.length(), yIntersect.length());
	} else {
		return new Rectangle.NO_RECTANGLE;
	}
}
</code></pre>
