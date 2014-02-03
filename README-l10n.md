TYPO3 Neos localization branch (experimental)
=============================================

Testing the l10n features
-------------------------

The current state of development is mainly inside the content repository (TYPO3CR) and adjustments in Neos to provide
a localizable routing and handling of nodes with additional locale information. There is no special UI for handling
localized content yet!

Anyway, a simple editing workflow for multi-locale content already works by just changing to the particular locale
version in the frontend.

Set up
~~~~~~

Migrate your database:

    ./flow doctrine:migrate

Prune an existing site (we are working on a seamless update):

    ./flow site:prune --confirmation TRUE

To get started the Neos demo site should be imported:

    ./flow site:import --package-key TYPO3.NeosDemoTypo3Org

As a default every node will be assigned to the "mul_ZZ" locale which is the default value in the context. To create
new variants of the nodes in another you can use a simple command for now:

    ./flow node:translate "/sites/neosdemotypo3org" TRUE "mul_ZZ" "de_DE"

This will create a new variant in the locale "de_DE" based on the "mul_ZZ" content (which is the default). The command
also allows to translate individual nodes and subtrees.

For the routing in Neos you need to specifiy identifiers that should be used to access the content in different locale
versions from the outside. Every identifier maps to an ordered list of locales that will be used as a fallback (the
first locale identifier has a higher priority than the last).

Inside `Configuration/Settings.yaml` or your site package:

    TYPO3:
      Neos:
        localization:
          localeMapping:
            de: [de_DE, de_ZZ, mul_ZZ]
            en: [en_ZZ, mul_ZZ]
            us: [en_US, en_ZZ, mul_ZZ]

Note: This configuration is subject to change.

You should now be able to access your frontend by appending the locale fallback chain identifier to the URL (e.g.
http://localhost/de/).

If you change to the Neos backend you might need to update the URL in the address bar to use the correct locale.


Run the Behat scenarios for TYPO3.TYPO3CR
-----------------------------------------

The TYPO3CR l10n features are specified with Behat and serve as the acceptance tests for the implementation.

Create a development configuration for Behat in `Configuration/Development/Behat/Settings.yaml`:

    TYPO3:
      Flow:
        persistence:
          backendOptions:
            dbname: 'neos_testing_behat'

Create a testing configuration for Behat in `Configuration/Testing/Behat/Settings.yaml`:

    TYPO3:
      Flow:
        persistence:
          backendOptions:
            dbname: 'neos_testing_behat'
            driver: pdo_mysql
            # Adjust user and password
            user: ''
            password: ''

Run all the scenarios in the TYPO3.TYPO3CR package:

    bin/behat -c Packages/Application/TYPO3.TYPO3CR/Tests/Behavior/behat.yml.dist
