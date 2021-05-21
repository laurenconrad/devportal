## WooCommerce Install Troubleshooting

Categorizing WooCommerce Installation issues along with troubleshooting to resolve. This will eventually be part of a script that we run during installation of the WooCommerce plugin. These steps are what customers can do to resolve issues on each step.

[TOC]

#### Platform credentials not in Database {#platform-credentials-not-in-database}



*   Known Causes: User skipped WooCommerce plugin install during onboarding step
*   Troubleshooting
    *   Ask the seller to re-onboard and ensure the final step of installing the WooCommerce plugin is completed. Verify that the app-id is visible post-install on the onboarding page post-install.



<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image1.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image1.png "image_tooltip")


<p id="gdcalert2" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image2.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert3">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image2.png "image_tooltip")


*   Internal notes: There are some other forms of app id missing errors that we need to figure out the root cause of. This only affects errors where the platform credentials are missing from our identity databases.


#### Authorization headers dropped {#authorization-headers-dropped}


*   Known Causes:
    *   Headers dropped due to a proxy intercepting the headers on the seller side.
*   Troubleshooting:
    *   One way to verify whether the seller’s site is indeed dropping auth headers is to send a request with a dummy header and verify the same headers are being returned back. A prerequisite for this test is that the store has the Fast plugin installed.
    *   Example request:
        *   curl --location --request GET 'https://&lt;store url>/wp-json/wc/fast/v1/authecho' --header 'Authorization: TEST'
    *   If it is confirmed that the seller’s host server is dropping headers, they should reach out to their IT team to resolve this issue. We sometimes see this is caused due to a generic WP API issue for sellers using Apache servers for their services. One suggested approach is to modify the .htaccess file as described in this comment: [https://github.com/WP-API/Basic-Auth/issues/35#issuecomment-244001216](https://github.com/WP-API/Basic-Auth/issues/35#issuecomment-244001216) 
*   Internal notes: Check from wp-admin that the api keys used are present in the wp-admin > woocommerce > settings > advanced > REST Api section. Compare the last digits of the key displayed with the one we are using.


#### Plugin compatibility {#plugin-compatibility}


*   Known issues with 3rd party plugins
    *   No CAPTCHA reCAPTCHA for WooCommerce: May prevent user creation from rest api. Go to plugin settings and disable the ‘User Registration’ section to fix.
    *   Wt-smart-coupons-for-woocommerce: Interfering with Coupon code
    *   Hide My WP: Might cause problems with REST API Access by changing the default wp-json to a custom path, Also can disable API access for not logged in users or all users. Need to adjust configurations and it should be fine.
    *   Divi Shop Builder
    *   Shopper Approved
    *   WOOCS


#### Unsupported shipping destination {#unsupported-shipping-destination}


*   Known Causes: Shipping address is not supported for certain international destinations
*   Troubleshooting
    *   We currently refer to the shipping settings on WooCommerce page to get acceptable shipping destinations for Fast. Please adjust your settings on ‘WooCommerce > Settings > Shipping’ to include the destination that your store supports. 


#### Buttons not appearing on PDP {#buttons-not-appearing-on-pdp}


*   Known causes:
    *   We currently don’t support add-ons. We do support variations/options, so if there is a way the seller can configure their product details with variations on their admin page, that would resolve the issue
    *   We do not support subscriptions at the moment.


#### Store root address not properly configured {#store-root-address-not-properly-configured}



*   Known causes: Currently happens when store root addresses are misconfigured (either as a mistake during onboarding or because of redirects) 
*   Troubleshooting: This currently requires a manual correction of platform credentials directly on our database.


#### Request timeout {#request-timeout}



*   Known causes: API calls sometimes timeout on seller servers due to poor hosting environments on seller side. Currently the order endpoint which takes the longest is the CreateAndAddToCart having a timeout of 30 seconds. If we can’t finish all external requests during that period we are timing out and the context gets cancelled any ongoing requests will error out.
*   Troubleshooting: If the problematic store has some endpoints constantly taking 5+ seconds per request we have to reach out to them and try to see what is wrong on their side. It is really hard to get acceptable user experience with that kind of wc api performance. We have internal dashboards to track request latencies per store to get a better understanding of their hosting environments.
*   Internal note: We need to check whether there are other reasons we’ve seen for timeout issues, and whether we can lengthen it on a per-case basis