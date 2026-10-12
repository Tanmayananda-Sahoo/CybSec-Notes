# OAuth 

## What is it?
- OAuth is liking logging in to a website through a different website. Its like sharing a part of your data without actually exposing your login credentials.

## OAuth Grant Types
1. Authorization code grant type
- In this when the client application asks whether user is agreeing to share the asked resources and if user agrees, then the client application gets a authorization code which it exchanges with OAuth service to get the access token which it can use to make API calls and all. All the communication between server-to-server takes place with the help of a secure communication channel that is not visible to the end user. Now this channel is established when client application registered with the OAuth service, so it gets a client-secret which it uses to authenticate itself in the communication channel. This is argaubly the most secure way.

- 