# TagDebt Bot

TagDebt identifies technical debt in issues, using machine learning (ML), and automate the labeling process to support issue management and prioritization.

The bot analyzes the content of issues (titles, descriptions), identify self-admitted technical debt, and labels issues using two main tags (stad, and non-satd). Also, TagDebt is able to send email notifications, enabling more efficient issue tracking and management. This bot is particularly useful for large projects where manual issue tracking and categorization can be time-consuming.

## Installing the bot on a repository

To install the bot, go to our <a href="https://github.com/apps/tagdebt-bot" target="_blank">page on GH Marketplace</a> and click on Install. During the installation process, you can select the repositories in which the bot will operate.

![image](https://github.com/user-attachments/assets/c6e6b4ca-fd1f-4317-9833-3fbe033f8907)

## Configuring the bot

TagDebt bot operates based on a configuration file. You can either setup your own configuration file, or let the bot use a default one.

### Setting your own configuration file

To configure the bot, you may create a folder named `Bot`, and upload a file named `config.json` to this folder. This file contains the configuration options that will be used by the bot to label issues and send notification. The default configuration file can be found [here](https://github.com/biazottoj/issue-classification-bot/tree/main/Bot/config.json), and you can download it and upload to your repository. Please, refer to [this repository](https://github.com/biazottoj/bot-evaluation) as an example of how the folder and file should look like. 

You can now edit the `config.json` to configure the bot (e.g., using Github's document editor) and set your preferences. Below in this quick-start guide, you will find the main options for configuring the bot. To check all available options, please refer to the [Documentation page](https://github.com/biazottoj/issue-classification-bot/wiki/Documentation#configuring-the-bot).

### Using a default configuration file

Although having your own configuration file within a repository might increase the flexibility and usefulness of the bot, our bot is able to run without adding a configuration file to your repository. In this scenario, we use a default configuration file which is stored within our bot source code. The default configuration file can be found [here](https://github.com/biazottoj/issue-classification-bot/tree/main/Bot/config.json). Also, in the next sections we inform the default values of each configuration option.

## Available commands

Interact with the *Issue Classification Bot* by using the following commands in the comments of a GitHub issues:
- `/tdbot label`: Automatically labels an issue using the ML model. The bot can automatically assign two labels:
  * "satd": indicates that the bot identified TD in the issue.
  * "non-satd": indicates that the bot did not identify TD in the issue.
- `/tdbot label <label>`: Assign the specified label to the issue.
- `/tdbot help`: Displays a help message with command details.

## Configuring (auto) labeling
- `auto-label`: *Boolean* to set if the bot should automatically label new issues. (*default: false*)
- `payload-type`: refers to which fields will be analyzed by the bot to identify TD. You can choose between (*default: description*):
  * "title":       to set whether the bot's ML model should generate a label based on the title of the issue
  * "description": to set whether the bot's ML model should generate a label based on the description of the issue
  * "merged":      to set whether the bot's ML model should generate a single label based on the title and the description of the issue
  * "both":        to set whether the bot's ML model should generate two separate labels based on the title and the description of the issue

[Here is an example](https://github.com/biazottoj/bot-evaluation/issues/3) of a the command `/tdbot label` based on the issue description.

## Configuring notifications
- `send-emails`: *Boolean* to set if the bot should send email notifications (*default: true*)
- `when-to-send`: Choose between (*default: all*):
  * "label":     to set whether the bot should send email notifications when an issue is labeled
  * "lingering": to set whether the bot should send email notifications when lingering issues have been identified in the repository
  * "all":       to set whether the bot should send email notifications for all the above scenarios
- `email-info`: Configuration options for the email sender
  - `which-labels`: if the bot should send email notifications when it adds a label to the issue (by setting `when-to-send` to "label" or "all"), choose between (*default: specific*):
    * "all":      if the bot should send email notifications for all kinds of labels
    * "except":   if the bot should send email notifications for all kinds of labels except the ones specified in `except-labels`
    * "specific": if the bot should send email notifications only for the labels specified in `specific-labels`
  - `except-labels`: the labels for which the bot does not send email notifications, specified as a *list of strings*: \["label1", "label2", ...]
  - `specific-labels`: the labels for which the bot sends email notifications, specified as a *list of strings*: \["label1", "label2", ...] (*default: ["SATD"]*)
 - `recipients`: the list of email addresses of contributors that should receive email notifications, specified as a *list of strings*: \["emailAddress1", "emailAddress2", ...]

[Here is an example](https://drive.google.com/file/d/1rPg2f7LevPTln1DhPHE_JjmGn5dpr7Yp/view?usp=sharing) of an email sent by the bot based on a label.

## Examples

### Configuring the bot to automatically assign labels to issues.
To configure the bot automatically assign a label when a issue is created, you can alter the `auto-label` option on the configuration file:
```JSON
"auto-label": true
```
With `auto-label`: true , issues such as the following would receive the label "SATD":

> Summary: camel-http - does not remove httpClient.xxx URI options\
Description: When using the options to configure httpClient using URI option, they should be removed from the uri that is left over to the HTTPProducer. should remove the httpClient.xxx so it's 

While issues such as the following would receive the label "non-satd"

> Summary: Opcodes.thrift missing from git repo\
Description: It seems that Opcodes.thrift definition (required by Exprs.thrift in spec of TExprNode) is missing from git repository, causing project build failure.

### Configuring notifications based on labels assigned to issues.
Configuring notifications for cerain labels might help to keep track of specific problems. For instance, to keep track and receive emails for issues with the label "SATD," the following options could be used:
```JSON
"send-emails": true,
"when-to-send": "label",
"email-info": {
   "which-labels": "specific",
   "specific-labels": ["SATD"],
   "recipients" : [
      "contributor1@gmail.com",
      "contributor2@yahoo.com"
    ]
   "email-body-template": {
      "label": "Hi,\n\n/issue_label has been identified in issue #/issue_number",
   }
}
```

With the previous configuration, issues such as the following would trigger e-mails (i.e., the label SATD would be assigned to it):

> Summary: Support using OperationAttributes through shell script\
Description: Currently the ruby scripts for Put does not support setting of Operation Attributes through shell. It may be useful in some cases and also for testing. And that would give a full fledged support using shell.

While issues such as the following would not trigger e-mails (i.e., non-satd label):

> Summary: FileNotFound on DFS block file\
Description: While run the sort benchmark a reduce failed with: (No such file or directory) at Method).
