---
title: Nutanix Playbooks - Telegram Bot 
tags: HCI Nutanix
date: 2020-05-20 00:00:00 +0200
---

In this new post, I will describe how to send a notification to your Telegram via a Telegram Bot using Nutanix Playbooks.

I recently got this idea by watching the Nutanix University video [here](https://www.youtube.com/watch?v=KmS5jCbQV40 "Nutanix WeChat") describing how to send notification to WeChat.

As this is not popular in Europe, and also because I am a big fan of Telegram, I will describe the procedure in this post.

## Create a Telegram Bot

In order to create a Telegram bot, just chat the BotFather (@BotFather) and ask him to create a new bot with the command : /newbot 

It will ask you for a botname and bot username.

![Telegram Bot](/assets/images/telegrambot.jpg)

Once the bot has been created, the BotFather will provide the token to use the HTTP API. This one is important.

## Get the Chat ID

We are going to create a group on Telegram. This is the only easy way I found to get the chat_id.

Create a new group and call it whatever you want, insert your new bot user and create a chat.

![Create Group](/assets/images/creategroup.jpg)

Now, click in your group and look at the URL in your web browser. This is your chat ID (the digits only)

![Chat ID URL](/assets/images/chatidurl.jpg)

## Test it

In order to confirm that we have all the info required, we can run a quick test with this online API tester [here](https://reqbin.com "API Tester") .

First, we need the URL. To send a message with Telegram via the bot, the URL needs to be this way :
{: .text-justify}
<https://api.telegram.org/botAPITOKEN/sendMessage>


Remember, the Telegram bot provided an APITOKEN, you just need to add the word "bot" before the token, here is an example:

<https://api.telegram.org/bot123456789:XXXXXYYYYYYYY/sendMessage>

Secondly, we need to specify that we are going to use JSON and provide the Chat ID we found in the test above.
{: .text-justify}

The chat ID needs to have the minus sign before the digits.

Select the POST action and click Send. If this worked, not only you will get a message in the Telegram Group, you also will get a 200 status response.
{: .text-justify}

![Test API](/assets/images/testapi.jpg)

## Nutanix Playbook

The harder has been done ;) You are now going to use Prism Central, create a Playbook (manual or alert), and trigger a REST API Action.
{: .text-justify}

In the menu, click Operations,Playbooks and create a Playbook.

![Playbooks menu](/assets/images/menu.jpg)

For this demo, I will use a Manual Playbook as it is quicker. But you can create the same action based on an Alert.
{:.notice--info}

Select the trigger (Manual) and select an entity type (VM).

![Trigger](/assets/images/manual.jpg)

Click on Add Action, search for API and use REST API.

![Action REST API](/assets/images/restapi.jpg)

Here is the parameters you need to enter :

<pre>
Method : POST
URL : https://api.telegram.org/bot123456789:XXXXXYYYYYYYY/sendMessage
Request Body : {"chat_id":"-462316723", "text":"Alert!"}
Request Headers : Content-Type:application/json;charset=utf-8
</pre>

![Parameters REST API](/assets/images/parametersapi.jpg)

Save, Enable and That's it! 

![Save Playbook](/assets/images/saveplaybook.jpg)

You are now ready to pull the trigger and try it out!

## Test the playbook

Now go to your VMs, select any, click on Action and Run Playbook. Select your playbook and click Run!
{: .text-justify}

![Run Playbook](/assets/images/runplaybookvm.jpg)

![Select Bot](/assets/images/selectbot.jpg)

Did you get a notification ? Yes ? Well done! 

![Notification](/assets/images/alertontelegram.jpg)

No ? Go to the menu, click on Plays and you will be able to check if it failed.

![List of Plays](/assets/images/playslist.jpg)

Obviously, I created the easiest message you can get. But you can personalize this message or alert by using the Parameters option in your request body (to get the VM Name for example).
{: .text-justify}

![Add VM Name](/assets/images/parameters.jpg)