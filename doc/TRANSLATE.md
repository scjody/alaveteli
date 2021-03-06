The software translations are implemented using GNU gettext, and the
resource files are managed in Transifex.

The Transifex project is at
https://www.transifex.net/projects/p/alaveteli/; you'll probably want
an account there (ask on the mailing list).  It has a fairly easy to
use interface for contributing translations.

# Translation process: translator's view

When a developer adds a new feature to the user interface in
Alaveteli, they use some code to mark sentences or words ("strings")
that they think will need to be translated.

When the Alaveteli release manager is planning a release, they upload
a template containing all the strings to be translated (called a POT)
to Transifex.  This causes your own translations in Transifex to be
updated with the latest strings.

When you visit Transifex, it will prompt you to fill out values for
all new strings, and all strings that have been modified.  In the case
where a string has only been slightly modified, such as with
punctuation ("Hello" has become "Hello!"), Transifex will suggest a
suitable translation for you (look for the "suggestions" tab under the
source string).

In order for this feature to work properly, the release manager has to
download your translations, run a program that inserts the
suggestions, and then upload them again.  Therefore, when a release
candidate is announced, make sure you have uploaded any outstanding
translations, or you will lose them.

When a release candidate has been annouced, there is a **translation
freeze**: during this period, developers must not add any new strings
to the software, so you can be confident that you're translating
everything that will be in the final release.

The release manager will also give you a **translation deadline**.  After
this date, you can continue to contribute new translations, but they
won't make it into the release.

## General notes on translation in Transifex

Some bits of text will have comments attached to them from the Alaveteli
application developers about the context in which the text appears in the
application - these comments will appear under the 'Details' tab for the
text in Transifex.

Some bits of text are in the form of two bits of text separated by a `|`
character e.g. `IncomingMessage|Subject`. These represent attribute names, so
`IncomingMessage|Subject` is the subject attribute of an incoming message on
the site. You should not prioritise these types of text when translating -
they do not appear on the site anywhere at the moment, and when they do,
they will only be used in the admin interface. If you do translate them, only
translate the text after the `|`.

# Translation process: release manager's view

Before the Alaveteli release manager cuts a new release branch, they
must:

* pick a date for the release branch to be cut ("release candidate date")
* make an announcement to the translators (using the "announcements"
  feature in Transifex) that they should ensure they have any pending
  translations saved in Transifex before the release candidate date
* make an announcement to the developers that all new strings should
  be committed before the release candidate date
* on the release candidate date:
    * download (`tx pull -a -f`) and commit all the current translations (important:
      there's no revision history in Transifex!)
    * you should also commit these translations to a hotfix for the
      previous version, so they are preserved against the last known
      good msgids
    * regenerate the POT file and individual PO files for each
      language, using `bundle exec rake
      gettext:store_model_attributes`, followed by `bundle exec rake
      gettext:find`
        * careful of including msgids from themes in `vendor/plugin`;
          you might want to move them out of the way before running
          the above commands
        * this updates the PO template, but also merges it with the
          individual PO files, marking strings that have only changed
          slightly as "fuzzy"
    * reupload (`tx push -s -t`) the POT and PO files to Transifex from the
      current release branch
        * The point of uploading the PO files is that Transifex
          converts the "fuzzy" suggestions from Transifex into
          "suggestions" under each source string
        * Note that Transifex *does not* preserve fuzzy strings in the
          PO files it makes available for download, on the grounds
          that Transifex supports multiple suggestions, whereas
          gettext only allows one fuzzy suggestion per msgid.
    * remove the fuzzy strings from the local PO files (because they
      make Rails very noisy), and then commit the result. You can do
      this by re-pulling from Transifex.
* on the release date:
    * download and commit all the current translations to the current
      release branch

# Translations: developers' view

See the [I18n guide](https://github.com/mysociety/alaveteli/wiki/I18n-guide) on the wiki.
