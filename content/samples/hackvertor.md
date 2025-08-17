---
title: 'Hackvertor Writing Task'
weight: 3
---
This is a writing sample I created to demonstrate my technical writing skills. The task was to create a tutorial of how to install and use Hackvertor, which is an extension for Burp Suite.

For context, Burp Suite is a web security testing tool used by security professionals and ethical hackers to identify vulnerabilities.

---

# Hackvertor
Hackvertor is a tag-based conversion tool that you can add as an extension to Burp Suite. It supports various encodings including HTML5 entities, hex, and URL encoding.  

When you encode with Hackvertor, it inserts tags around the text and then automatically converts it when you send the request. This allows you to continue working with a payload in its unencoded form, making it easier to read and removing the need to manually decode and re-encode the payload each time you modify it. 

## Installing Hackvertor
You can add Hackvertor to Burp Suite via the BApp Store. To install Hackvertor:
1. Go to **Extensions > BApp Store**.
1. Select **Hackvertor** from the list of extensions. You can use the search bar to find it quickly.
1. Click **Install**.  

Hackvertor is now installed and ready to use.

## Using Hackvertor to Encode an SQL Injection Payload
This tutorial demonstrates how to use Hackvertor to encode a payload. You will then use this payload to test the vulnerability of a fake shopping website to SQL injection.

### Access the Website
First you must open the target website and perform an action on it.
1. Go to **Proxy > Intercept** and click **Open browser**.
1. In the browser, go to the following URL: `https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-non-oracle`
1. Click **Access the lab**. 
1. If prompted, log in with your credentials.
1. At the top of the **WE LIKE TO SHOP** webpage, click the **Tech gifts** filter.

{{< note >}}
When you interact with the website, Burp Suite Community Edition shows the requests and responses between the browser and the host server. It also shows you the host, method, URL, parameters, and other information for each request.
{{< /note >}}

Now that you have performed an action on the target website, the next step is to create the payload.

### Add the Payload
You need to create an attack payload to test if the filter parameter is vulnerable to SQL injection. 
1. In Burp Suite, go to **Proxy > HTTP history**.
1. Select the row where the URL column contains `/filter?category=Tech+gifts`.
1. Right-click the row identified in the previous step and select **Send to Repeater**.
1. Go to the Repeater tab. On the first line of the request, triple-click the parameter value **Tech+gifts** to select it.
1. Replace the selected text with the following payload: 
    ```
    ' UNION SELECT table_name, NULL FROM information_schema.tables`
    ```

This is your attack payload. If the website is vulnerable to SQL injection, it retrieves the names of tables from the database.

### Encode the Payload Using Hackvertor
You cannot send the payload in its current form because it contains invalid characters for a URL query string. Therefore, you must first encode the payload.
1. Triple-click the payload you added in the previous steps.
1. Right-click the selected payload and select **Extensions > Hackvertor > Encode > urlencode**. This wraps the text in `<@urlencode>` tags.
1. Click **Send** to send the request.

All the text between the tags is automatically encoded by Hackvertor when the request is sent.

### Review 
Finally, you can verify that the request was sent to the server and review the response received. To do this, go to the Logger tab, scroll to the bottom of the list, and select the most recent request.

By looking at the request, you can see that it was sent with the text URL-encoded. If you select this encoded text, the original decoded text you inserted is shown on the Inspector panel on the right-hand side of the screen.
 
![Example Response](/writing-portfolio/images/hackvertor.png)

If you look at the response, you can see that it contains a list of table names that have been extracted from the database. This confirms that the website is vulnerable to SQL injection. 

You could now continue the attack to further investigate the extent of this vulnerability you have discovered. For example, you could try to extract data from the tables to see if they contain sensitive information. As you used Hackvertor to encode the payload, you can easily edit the original unencoded text and then send a new request, without needing to do any manual decoding or encoding.
