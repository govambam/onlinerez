# Static Resume Website + Twilio App

Here are a few notes/steps on how to get your application (pun intended) noticed at Twilio. 

## Getting Started

Every employee at Twilio has made a Twilio app. In this tutorial we'll get your application on a website hosted by Bitballoon, integrated with Rollbar, and add a Twilio SMS workflow for good measure. Little to no coding required. 

In the end, we'll have created a digital resume website that alerts you via SMS from Twilio any time someone has a) viewed your website, and b) viewed your website for more than 10 seconds. We'll be using a Rollbar to Zapier to Twilio flow to accomplish this. 

### Create Accounts

First, we'll need to sign up for a few free trials. Just go ahead and sign up for these, and confirm your identity through email. We'll start using 'em later. 


* [Github](https://github.com/join) - For version control and duh. 
* [Twilio](https://www.twilio.com/try-twilio) - Also duh.
* [Zapier](https://zapier.com/sign-up/) - This'll make it much easier to get Rollbar to talk to Twilio. No code required.
* [Rollbar](https://rollbar.com/signup/) - Honestly this should have been at the top. 
* [Bitballoon](https://www.bitballoon.com/login) - This will host your resume online. Easy. Also if you did these in order you could have created your account using your Github credentials.  



### Downloads

Now we'll need to download and install a few things. 

* [Atom.io](https://atom.io) - Awesome text editor. This one is pretty self exmaplanitory. 


## Getting Started (for real this time)

Now that we've got all of our accounts set up, let's clone the repo with our website template. 


```
https://github.com/RyanFitzgerald/devportfolio
```

Open the Command Pallete in Atom and search `Git Clone`. When prompted, paste the above URL and continue. This should open the repository files in Atom. 


## Installing Rollbar

Go back to rollbar.com and select `Browser JS` from the options on the left. Copy the code snippet that appears on the right, and open Atom again. 

Navigate to the `index.html` file. At the top of the file, find the `<header>` section. Paste the Rollbar code snippet directly under the `<header>`.Save the file and this should intitialize Rollbar on the page and error data should be sent to the project right away. 

To test that the integration worked, go back to the Finder and open `index.html` in Google Chrome. Open Chrome Dev Tools ( `ctrl` + `shift` + `I` on PC) and open the Console tab. Paste the following and hit enter:

```
window.onerror("TestRollbarError: testing window.onerror", window.location.href)
```

Go back to your Rollbar project and refresh the page. If the integration worked, you should see your first error in your Rollbar dashboard.

## Creating a Zap

Go back to Zapier and search for Twilio Zaps. Use the `Send webhook notifications as an SMS message with Twilio` Zap. Verify that the How It Works secion reads as follows: 

```
Zapier catches the webhook
Zapier sends information from the webhook via SMS through Twilio
```

If so, go ahead and create that Zap. Continue until you've reached the `Test Webhooks by Zapier` window and copy the webhook URL to your clipboard. Then in a new tab, naviate to your project settings in Rollbar to create a Webhook notification rule.

```
https://rollbar.com/ACCOUNT_NAME/PROJECT_NAME/settings/notifications/
```

Paste the Zapier Webhook URL in Rollbar and save settings. Delete all the existing Rules to keep things, and then from the Template dropdown, select `Every Occurance -> Post to Webhook` and then click `Add`. Click `Send test occurance` in Rollbar and then head back to Zapier to continue. You should see `Test Successful` before you continue. 


Continue in Zapier and connect your Twilio account by inputing your Account SID and Auth Token. This can be found at:

```
https://www.twilio.com/console
```

You should have created your first SMS telephone number during your Twilio account set up. If not, go back to your Twilio Dashboard and do so. Once ready, go back to Zapier and select your number in the `From Number` dropdown. If it does not appear, you can select `Use a Custom Value` and paste your phone number here. 

The `To Number` should be your personal cell phone number with your country code. 

And the Message should be whatever text you want to appear when someone views your resume website. We're going with `Hey-- you're doing great!`. 

Continue and then test. You should have just received a text from Twilio reading the custom message you created. If everything looks good, click Finish. 



## Configure Rollbar Messages

Now that the basic integration is set up, let's make some more advanced rules in Rollbar so your Twilio SMS messages are more relevant and exciting. 

In Atom, go to the Rollbar script in your header. Directly under it, paste the following script: 


```
<script> Rollbar.log("hey you're doing great");
function startClock () {
  Rollbar.log("niceeee they've been looking for 10 seconds!'");
}
setTimeout("startClock()", 10000);
</script>
```

What we've done here is made it so each page load will throw an error with the message `he you're doing great`, and if someone has been on the page for 10 second, a second error will be thrown with the message `niceeee they've been looking for 10 seconds!`. These will appear as two different items in your Rollbar account. To test, save your index.html file and reload the page in Google Chrome. Stay on the page for 10 seconds and go back to check your Rollbar dashboard. You should see these two items in your account. 

Now go ahead and set the severity of the `hey you're doing great` item to `debug` and the `niceeee they've been looking for 10 seconds!` item to `warning`. This will affect the SMS messages. 

Now go back to your notifications at: `https://rollbar.com/ACCOUNT_NAME/PROJECT_NAME/settings/notifications/`

Add a new filter to the rule you've created so that the `Level` must `==` `debug`. Now on every page load, you will receive the original message from the Zap you created. 

## Create a 2nd Zap

Now that we are getting an inspiring text after each page load, we'll hvae to create a second webhook so you can know when someone stays on the page for longer than 10 seconds. Go through the same process as earlier and create a Zap that does the following: 

```
Zapier catches the webhook
Zapier sends information from the webhook via SMS through Twilio
```

This time, you'll take the newly generated webhook URL, and use that in a second webhook notification rule in Rollbar. Go through the process of greating the Zap, but copy that URL to your clipboard and save it for later. 

The `From Number` and the `To Number` should be the same as earlier, but this time the message should be something about the viewer staying on your page for >10s. We're going with `Wow! These guys must be really interested in you!`. Save the Zap. 

Now go back to your notification settings in Rollbar, and create a new `Every Occurance -> Post to Webhook` rule. This time, paste that second wehbook URL as in the rule so we aren't using the default webhook. Add a filter to the rule that the `Level` must `==` `warning`. Now whenever this rule is triggered, you'll get the SMS message from the second webhook. 

