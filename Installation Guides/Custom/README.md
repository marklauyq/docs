# Installation and Setup
This guide will be able to help walk you through on how to get started with installing Inncee onto your custom website.

* [1.1 Create Account](https://github.com/inncee/docs#11-create-account)
* [1.2 Install your tracking script](https://github.com/inncee/docs#12-install-your-tracking-script)
* [1.3 Tracking Events](https://github.com/inncee/docs#13-tracking-events)
* [Event Setup](https://github.com/inncee/docs#event-setup)
* [Automatically Tracked Events](https://github.com/inncee/docs#automatically-tracked-events)
* [Backend Events](https://github.com/inncee/docs#backend-events)
* [Prerequisites](https://github.com/inncee/docs#prerequisites)
* [ORDERS API](https://github.com/inncee/docs#orders-api)
* [CUSTOMER API](https://github.com/inncee/docs#customer-api)
* [PRODUCTS API](https://github.com/inncee/docs#products-api)
* [CATEGORY API](https://github.com/inncee/docs#category-api)
* [CATEGORYMAP API](https://github.com/inncee/docs#categorymap-api)

### 1.1 Create Account 
Create an account at https://beta.inncee.com. This allows our system to generate:

-	a unique account ID
-	a tracking script code to install on your store
-	and a secret key to allow you to authenticate the JSON data that you can send to our API endpoints

Once generated all this information will be sent to you via email.

Note: Ignore the pop-up that asks you to connect your store to Shopify.

### 1.2 Install your tracking script
You will need to install our tracking script library after the opening of the <head> tag found in every
page of your website.

```javascript
<script type="text/javascript"
src="https://shopify.inncee.com/tracker/YOUR_ACCOUNT_ID?shop=YOUR_STORE_URL"></script>
```

Once the script has been installed, you’ll be able to start recording events on your website.

### 1.3 Tracking Events
For our platform to be able to analyze your data and look for information, we need to be able to record
events that happened on your website. Some events are automatically tracked while others requires
you to install code snippets manually or calling our webhook end points in our app to send data to us.

#### Event Setup
Install these code snippets into your online store to begin tracking the following events.

<html>
<body>

<table>
  <tr>
    <th>Event</th>
    <th>Description</th> 
    <th>Code</th>
  </tr>
  <tr>
    <td>User logged in</td>
    <td>
		Required. This code block helps us to identify visitors when they are logged in. It should be included on every public page of your website when the user has logged in
		<br /><br /> It can be omitted if the user is not logged in 
		<br /><br /> innceeID * – Your unique account ID
    </td>
    <td>
		<pre>
		<code>
		analytics.identify(
		'&lt;USERS_ID&gt;',{'innceeID':
		'&lt;YOUR_INNCEE_ACCOUNT_ID&gt;'});
		</code>
		 </pre>
    </td>
  </tr>
  <tr>
    <td>Add to cart</td>
    <td>Required. This code block records the event whenever a user clicks on the add to cart button. It should be included on every page that has an “Add To Cart” button
    <br /><br />innceeID * – Your unique account ID
    <br /><br />categoryID * - The unique ID of your category
    <br /><br />productID * – The unique ID of the product being added to cart variantID
    </td>
    <td>
    <pre>
    <code>
    analytics.track(
    “addtocart”,{‘innceeID’:
    ‘&lt;YOUR_ACCOUNT_ID&gt;’,
    ‘categoryID’:‘yyyyy’,
    ‘productID’:‘ppppp’,
    ‘variantID’: ‘’,
    ‘Price’: ’12.50’,
    ‘Quantity’: ‘1’,
    ‘Currency’: ‘$’}
    );
    </code>
     </pre>
    </td>
  </tr>
  <tr>
    <td>Checkout</td>
    <td>Required. This code block records the event when a user initiates a checkout. It should be included on every page that has a checkout button.
    <br /><br />innceeID * – Your unique account ID
    <br /><br />cartID * - A unique ID of the shopping cart being checked out
    </td>
    <td>
    <pre>
    <code>
    analytics.identify(
    '&lt;USER_ID&gt;',{'innceeID':
    '&lt;YOUR_ACCOUNT_ID&gt;,
    ‘cartID’ : ‘qqqqqq’}
    );
    </code>
     </pre>
    </td>
  </tr>
    <tr>
    <td>Order Completed</td>
    <td>Required. Records the event when an order has been completed
    <br /><br />innceeID * – Your unique account ID
    <br /><br />orderID * - The unique order ID
    </td>
    <td>
    <pre>
    <code>
    analytics.track(“OrderComplete”,{
    ‘innceeID’:‘&lt;YOUR_ACCOUNT_ID&gt;’,
    ‘orderID’:‘xxxxxx’}
    );
    </code>
     </pre>
    </td>
  </tr>
</table>

</body>
</html>

#### Automatically Tracked Events
There are some events that are automatically tracked by us when you first install our script. No further actions will be required from you.
- Page views
- Add to cart clicks
- UTM Campaign visits

#### Backend Events
The code snippets above help us to track events that happen on your online store. We will also need to track certain events that happen in the backend of your store as well.

We’ve setup a series of API end points that ready to receive JSON data from your store when certain backend events happen.

For example, when a new order has been created, you can send a JSON response with information about the new order to our API end point and our platform can begin storing that information for analysis.

##### Prerequisites
When making API calls to our servers, you are required to first generate a HMAC and attach it to your headers along with the timestamp used to generate it.

The recommended method for generating the HMAC using PHP is as follows:

```php
$timestamp = time(); 
$timestamp = time();
$secret = <OUR_SHARED_SECRET>;
$domain = "<OUR_DOMAIN.COM>";

$message = "store=".$domain."&timestamp=".$timestamp;
$hmac = base64_encode(hash_hmac('sha256', $message, $secret, true));
```

When making calls to our servers, attach the timestamp as a URL parameter.
```php
$ch = curl_init();

$url = "https://api.inncee.com/hooks/order/create?timestamp".$timestamp
curl_setopt($ch,CURLOPT_URL, $url);
```

the timestamp should be the same one used to generate the HMAC. 

Now attach the HMAC as a header with the key HTTP_X_HMAC_SHA256.

```php
$hmac_header = "HTTP_X_HMAC_SHA256:".$hmac
curl_setopt($ch,CURLOPT_HTTPHEADER, array($hmac_header));
```

Once done, you are now good to attach your data and send to our servers.
See the next section for a list of all our API end points and when you can send data to it.

