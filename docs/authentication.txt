##############
Authentication
##############

To allow users to authentication with BookBrainz, the webservice implements
OAuth 2. We use the password grant type, meaning that clients must forward
user credentials (username and password) to the website over HTTPS.

When a client successfully authenticates a user, they recieve an access token,
which must be sent in the header of every subsequent POST request.

Example requests are shown below, for a username "Jim" and password "Bob123".


Authentication Request:

.. code-block:: none

  {
    "client_id": "de305d54-75b4-431b-adb2-eb6b9e546013",
    "username": "Jim",
    "password": "Bob123"
  }

Authentication Response:

.. code-block:: none

  {
    "access_token": "f47ac10b58cc4372a5670e02b2c3d479"
    "refresh_token": "16fd27068baf433b82eb8c7fada847da"
  }

Subsequent POST:

.. code-block:: none

  HEADERS:
    Authorization: Bearer f47ac10b58cc4372a5670e02b2c3d479

  {
    "post": "data",
    "goes": "here"
  }
