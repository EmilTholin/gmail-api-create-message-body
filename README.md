# gmail-multipart-body-maker
Tiny utility function for creating a multipart-message-body that can be used for the Gmail API upload urls.

## Example usage

```js
const request = require('request-promise-native')
const fs      = require('fs')
const path    = require('path')

const createBody = require('./lib');

const catBase64 = fs.readFileSync(path.join(__dirname, 'cat.png')).toString('base64');
const dogBase64 = fs.readFileSync(path.join(__dirname, 'dog.jpg')).toString('base64');

var body = createBody({
  headers: {
    To: 'receiver@gmail.com',
    From: 'sender@gmail.com',
    Subject: 'This was rad, brother.'
  },
  textHtml: 'Thanks for last time, <b>buddy.</b>',
  textPlain: 'Thanks for last time, *buddy.*',
  threadId: '1536195a8ad6a354',
  attachments: [
    {
      type: 'image/jpeg',
      name: 'dog.jpg',
      data: dogBase64
    },
    {
      type: 'image/png',
      data: catBase64
    }
  ]
});

const responseString = await request.post({
  url: 'https://www.googleapis.com/upload/gmail/v1/users/me/messages/send?uploadType=multipart',
  headers: {
    'Authorization': `OAuth ${token}`,
    'Content-Type': 'multipart/related; boundary="foo_bar_baz"'
  },
  body: body
})

return JSON.parse(responseString)
```

## API


```js
/**
 * Creates a message body that can be used for the Gmail API simple upload urls.
 * @param  {object}   params
 * @param  {object}   params.[headers]            - Key-value object representing headers and their values
 * @param  {string}   params.[threadId]           - Id of the thread the message should be put into
 * @param  {string}   params.[textPlain]          - Plain text representation of the message
 * @param  {string}   params.[textHtml]           - Html text representation of the message
 * @param  {object[]} params.[attachments]        - Array of attachment objects
 * @param  {string}   params.attachments[].type   - Attachment type ('image/jpeg', 'image/png', ...)
 * @param  {string}   params.attachments[].[name] - Name of the attachment
 * @param  {string}   params.attachments[].data   - Base64 representation of the attachment data
 * @return {string}
 */
 createBody(params);
```

## Licence
MIT
