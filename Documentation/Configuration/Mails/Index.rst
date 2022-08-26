.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. include:: ../../Includes.txt

.. _mails:

Notification emails
===================

.. important::
   When you want to work with mail notifications, you should configure an absolute URL (like ``https://my-domain.com/``) in
   TYPO3's **site configuration** instead of a plain ``/``. This is required by TypoLink to create absolute URLs in mails.

Notification emails sent when a comment is submitted or published.

All configuration variables can also be implemented as TypoScript content
objects.

.. code-block: typoscript

    plugin.tx_pwcomments.settings {
        anyConfigurationProperty = TEXT
        anyConfigurationProperty.value = Some value
    }

.. _mails-general:

General configuration
=====================

===================================== ========
Property                               Type
===================================== ========
senderAddress_                         string
senderName_                            string
sendMailToAuthorAfterSubmit_           boolean
sendMailToAuthorAfterSubmitTemplate_   string
sendMailToAuthorAfterPublish_          boolean
sendMailToAuthorAfterPublishTemplate_  string
sitenameUsedInMails_  string
===================================== ========

.. _senderAddress:

senderAddress
"""""""""""""

.. container:: table-row

   Property
      senderAddress
   Data type
      string
   Description
      Sender's mail address when pw_comments sends out mails.

.. _senderName:

senderName
""""""""""

.. container:: table-row

   Property
      senderName
   Data type
      string
   Description
      Name to display in mail clients, instead of mail address. (optional)

.. _sitenameUsedInMails:

sitenameUsedInMails
"""""""""""""""""""
.. container:: table-row

   Property
      sitenameUsedInMails
   Data type
      string
   Description
      When this option is set, it replaces the getHostname() output, used in mail subject.

.. _pageTitle:

pageTitle
"""""""""
.. container:: table-row

   Property
      pageTitle
   Data type
      string
   Description
      The page title used in mails. Defaults to the contents of the
      :php:`$GLOBALS['TSFE']->indexedDocTitle` variable, which will also be set
      to contain the news article title.

Default configuration
---------------------

.. code-block: typoscript

    plugin.tx_pwcomments.settings {
        pageTitle = TEXT
        pageTitle.data = TSFE : indexedDocTitle
    }

.. _mails-to-author:

Mails to comment author
=======================

===================================== ========
Property                               Type
===================================== ========
sendMailToAuthorAfterSubmit_           boolean
sendMailToAuthorAfterSubmitTemplate_   string
sendMailToAuthorAfterPublish_          boolean
sendMailToAuthorAfterPublishTemplate_  string
===================================== ========

.. _sendMailToAuthorAfterSubmit:

sendMailToAuthorAfterSubmit
"""""""""""""""""""""""""""
.. container:: table-row

   Property
      sendMailToAuthorAfterSubmit
   Data type
      boolean
   Default
      0
   Description
      If this option is enabled, the author receives a mail after each comment he/she has written.

.. _sendMailToAuthorAfterSubmitTemplate:

sendMailToAuthorAfterSubmitTemplate
"""""""""""""""""""""""""""""""""""
.. container:: table-row

   Property
      sendMailToAuthorAfterSubmitTemplate
   Data type
      string
   Default
      EXT:pw_comments/[...]/mailToAuthorAfterSubmit.html
   Description
      Defines the path to the Fluid template which should be used for the e-mail to comment author.

.. _sendMailToAuthorAfterPublish:

sendMailToAuthorAfterPublish
""""""""""""""""""""""""""""
.. container:: table-row

   Property
      sendMailToAuthorAfterPublish
   Data type
      boolean
   Default
      0
   Description
      	If this option is enabled, the author receives a mail after a comment he/she has written has been published.
      	This option requires activated "moderateNewComments".

.. _sendMailToAuthorAfterPublishTemplate:

sendMailToAuthorAfterPublishTemplate
""""""""""""""""""""""""""""""""""""
.. container:: table-row

   Property
      sendMailToAuthorAfterPublishTemplate
   Data type
      string
   Default
      EXT:pw_comments/[...]/mailToAuthorAfterPublish.html
   Description
      Defines the path to the Fluid template which should be used for the e-mail to comment author.

.. _mails-to-others:

Notification mails to others
============================

When a comment is published, notification emails can be sent to article and page
authors, as well as authors of comments the user is replying to.

By default, you can enable mails to authors of comments the user is replying to
by setting the TypoScript constant
`plugin.tx_pwcomments.settings.sendMailOnCommentPublishToThread` to true.

You can also fully customize the recipients and notification emails in
TypoScript.

===================================== ========
Property                               Type
===================================== ========
sendMailOnCommentPublishTo_            string
sendMailOnCommentPublishTemplate_      string
===================================== ========

.. _sendMailOnCommentPublishTo:

sendMailOnCommentPublishTo
""""""""""""""""""""""""""
.. container:: table-row

   Property
      sendMailOnCommentPublishTo
   Data type
      string
   Default
      Emails to authors of comments the user is replying to.
   Description
      Email addresses that will be notified of new comments.

Example
-------

.. code-block: typoscript

    plugin.tx_pwcomments.settings {
        sendMailOnCommentPublishTo >
        sendMailOnCommentPublishTo = CONTENT
        sendMailOnCommentPublishTo {
            table = tx_news_domain_model_news

            select {
                pidInList = 0
                uidInList.data = GP:tx_news_pi1|news
            }

            renderObj = COA
            renderObj {
                # Add the email of the news article author
                10 = TEXT
                10 {
                    field = author_email
                    wrap = ,|
                }

                # Add the email of the backend user that published the news
                20 = CONTENT
                20 {
                    table = be_users

                    select {
                        pidInList = 0
                        uidInList.field = cruser_id
                    }

                    renderObj = TEXT
                    renderObj.field = email

                    stdWrap.wrap = ,|
                }
            }

            # Add the authors of the comment(s) this comment replies to
            stdWrap.dataWrap = |,{register : tx_pwcomments_threadEmails}
        }
    }

.. _sendMailOnCommentPublishTemplate:

sendMailOnCommentPublishTemplate
""""""""""""""""""""""""""""""""
.. container:: table-row

   Property
      sendMailOnCommentPublishTemplate
   Data type
      string
   Default
      EXT:pw_comments/Resources/Private/Templates/MailNotification/mailOnCommentPublish.html
   Description
      The template for the notification mail.
