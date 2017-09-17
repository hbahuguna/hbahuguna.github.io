---
layout: post
title: "Webdriver Design Patterns"
date: 2017-09-16
---
A test automation framework allows a user to write tests without dealing directly with the underlying test automation tool being used. A user should be able to focus on the business requirements when writing tests. Most of the times a user of a test automation framework is a tester who understands the product well and does not want to go into the technical details of the testing tool being used. So its important to note that test automation framework should provide test writers with an interface thats easy to use and hides the working of underlying testing tool.

When designing an automation framework we often encounter problems that require us to consider some specific Design Pattern. In this post I will discuss some design patterns that are worth considering.

<b>Component Object</b>

Modern web design enables web pages to be comprised of smaller components that can be used across the templates. A component itself can contain other components and can be contained by another component. We can also leverage a component object in automation framework which can then be inherited by the actual representation of components in automation. It would be logical to create a component object consisting of driver and the web element that identifies the component on the page. The components or the web elements inside the component should be searched with respect to the web element of the component, not from the top of page. So it makes sense to use findElement method of the web element inside the component rather than using findElement method of the driver. 

<pre class="highlight"><code>// Base class for all components
public class Component {
	private final WebElement element;
	private final WebDriver driver;
	public Component(WebElement element, WebDriver driver) {
		this.element = element;
		this.driver = driver;
	}
	protected WebElement findElement(By by) {
		return element.findElement(by);
	}
    protected T extends Component T findComponent(By by, Class T componentClass) {
        T component = null;
        try {
            Constructor T constructor = componentClass.getConstructor(new Class[] { WebElement.class, WebDriver.class });
            component = constructor.newInstance(findElement(by), driver);
        } catch (NoSuchMethodException | SecurityException | InstantiationException | IllegalAccessException | IllegalArgumentException | InvocationTargetException e) {
            e.printStackTrace();
            throw new IllegalArgumentException();
        }
        return component;
    }	
}
</code></pre>

<pre class="highlight"><code> 
// Google search page content component inheriting base component
public class ContentComponent extends Component {
    private final WebElement image;
    private final FooterComponent footer;
    public ContentComponent(WebElement element, WebDriver driver) {
        super(element, driver);
        this.image = findElement(By.tagName("img"));
        this.footer = findComponent(By.id("footer"), FooterComponent.class);
    }
    // ContentComponent contains FooterComponent 
    public FooterComponent footer() {
        return footer;
    }
    public WebElement image() {
        return image;
    }
}
</code></pre>

<pre class="highlight"><code>
// Google search page footer component 
public class FooterComponent extends Component {
    private final WebElement advertising;
    public FooterComponent(WebElement element, WebDriver driver) {
        super(element, driver);
        this.advertising = findElement(By.cssSelector(".fbar #fsl a"));
    }
    public WebElement advertising() {
        return advertising;
    }
}
</code></pre>


<b>Page Object</b>

Since a web page template is comprised of certain components, we can reflect this behaviour in automation by creating page objects for these templates. Now we can declare all the components that are needed on a certain template. This object which defines a particular template can then be used in tests to initiate a test on a particular instance of that template. We can first create a base Page class that can then be inherited by other Page Objects representing templates. This base Page class extends from Component class mentioned above because we would like to reuse findComponent method in our Page. We are assuming that the page has an HTML tag which contains head and body tags. We can treat body tag as a component which will contain other components on a page.

<pre class="highlight"><code>//Base class for a Page Object
public class Page extends Component {
    private final WebElement head;
    private final Component body;
    public Page(WebDriver driver) {
        super(driver.findElement(By.tagName("html")), driver);
        this.head = findElement(By.tagName("head"));
        this.body = findComponent(By.tagName("body"), Component.class);
    }
    public WebElement head() {
        return head;
    }
    public Component body() {
        return body;
    }
}
</code></pre>

<pre class="highlight"><code>
//GooglePage inherits Page
public class GooglePage extends Page {
    private ContentComponent content;
    public GooglePage(WebDriver driver) {
        super(driver);
        this.content = findComponent(By.className("content"), ContentComponent.class);
    }
    public ContentComponent content() {
        return content;
    }
}
</code></pre>

We are now ready to use the GooglePage Object created above in our test. Below is an example of a test that uses the objects using Page and Component objects :

<pre class="highlight"><code>public class GooglePageTest {
    @Test
    public void gTest() {
        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setBrowserName(BrowserType.CHROME);
        ChromeDriverService service = new ChromeDriverService.Builder().usingAnyFreePort()
                .usingDriverExecutable(new File(FileUtils.getUserDirectory(), ".venus/selenium/chrome/2.22/chromedriver")).build();
        ChromeDriver driver = new ChromeDriver(service);
        try {
            driver.get("https://www.google.com/");
            GooglePage gPage = new GooglePage(driver);
            WebElement advertising = gPage.content().footer().advertising();
            advertising.click();
        } finally {
            driver.quit();
        }
    }
}</code></pre>

This code can also be found at this <a href="https://github.com/hbahuguna/simpleGoogleTest">location</a>
