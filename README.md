# Helios Translation

This repository provides a translation template to ease the process of
translating _helios-server_ from Helios Voting (https://heliosvoting.org/)
to languages other than the default English. An example translation is also
provided (in Brazilian Portuguese).

## How do I use all this?
   You can download the _helios-server_ code found in here, install it
   according to original instructions and then apply the translation patch
   that can also be found here. It is not guaranteed this patch will work
   with newer Helios versions cloned directly from the original upstream;
   see further below as to why.

   Basically, all you need to do after cloning this repository is:
   ```console
   # cd helios-translation  // enter repository root
   # cp -r helios-server helios-server-copy  // always work on a copy
   # cd helios-server-copy  // enter Helios copied root
   # cp ../patch/translation.patch .  // copy translation patch to Helios root
   # patch -p1 < translation.patch
   ```
   You should now have a patched version of Helios Server that is ready to
   accept new translations. Of course, in order to make use of Helios you
   will still need to properly configure the server. Instructions and pointers
   on how to do that may be found in the Helios Voting website.

   The application of the translation patch already _tags_ (hopefully) all
   strings in the _helios-server_ code and provides an example Brazilian
   Portuguese translation under _helios-server/locale/pt_BR/_. Translations
   in other languages can be provided by creating a directory
   _<lang>/LC_MESSAGES/_ under _helios-server/locale/_, copying the _django.po_
   template from this repository in there and filling the translations inside
   the copied _django.po_.

   For example, to include an Helios translation to Dutch (nl), create
   directory _helios-server/locale/nl/LC_MESSAGES/_ and copy the _django.po_
   template in there. In code (via a console of your choice and from inside
   the _helios-server_ root):

   ```console
   # mkdir -p locale/nl/LC_MESSAGES/
   # cp /path/to/template/django.po locale/nl/LC_MESSAGES/django.po
   ```

   Now open the _django.po_ template using your favourite text editor and
   fill in all **msgstr** empty strings with text translations that
   correspond to the accompanying **msgid** English versions.

   **HINT:** leaving a _msgstr_ string as the default empty value makes
   Django use the same text as presented in _msgid_. You can use this
   to your advantage: if you find a text string that you deem not to need
   a translation to you language of choice (e.g. maybe words like "email"
   or "login"), simply leave the _msgstr_ string empty and Django will use
   the same value contained in the accompanying _msgid_.

   Finally, after filling in your translations, you will only need to compile
   your new _django.po_ file. This is easily done with the following command
   (make sure you are in _helios-server_ root):

   ```console
   # django-admin compilemessages
   ```   

   **IMPORTANT:** Django's translations are sensitive to the page language
   that your browser requests. That means Django will try to fetch the
   language your browser is requesting for web pages in order to present it
   to you. Meaning: **to test your own translations, make sure your browser is
   requesting Helios' pages in the language you translated for**. How to do
   this is highly dependable on the web browser you are using, but is usually
   easily configurable (at least for the most common browsers).

   **HINT:** for a reference on how the translations in the django.po file can
   be filled, the pt_BR/django.po file in this repository can be consulted.


## More Information

### The Good
   Helios is quite easy to translate! It uses the Django framework, which
   already provides facilities that ease the effort of translating Helios
   to pretty much any language. The strings in Helios can be easily tagged
   n code for translation. They are then united in a single file called
   _django.po_, inside which you can specify the individual string
   translations to the language of your preference.

### The Bad
   First of all, in order for any of this to work, every single string in
   Helios' code that is presented to the user needs to be _tagged_ in every
   single place it occurs. Unfortunately, the _helios-server_ raw code does
   not do that by default, meaning **you** will have to search the whole
   _helios-server_ code for every single occurrence of every single string
   that might be presented to the user and **tag** them in accordance to the
   Django translation specifications that can be found
   [here](https://docs.djangoproject.com/en/3.0/topics/i18n/translation/).
   Furthermore, after tagging all of those strings, you will still have to
   translate them, and there are many, many different strings (about 400!).

### The Ugly
   Helios' code also contains static files that are not touched by Django's
   translation machinery. This means those files cannot be translated via
   a django.po file; currently they need an individual and manual translation
   effort in the sense that translated versions of those static files need
   to be individually crafted for each language. File redirections to each of
   these different language versions are needed based on JavaScript code that
   checks the browser language preferences to load the correct file. These
   redirections have already been placed and should hopefully work for any
   language. Ugly, yes, but functional without much modification to the
   original code.

### String tagging and translation with a patch
   In order to ease the translation effort, a translation patch is provided
   here. Ideally, it already tags automatically the majority of the strings
   of the code. Additionaly, the patch already creates an example Brazilian
   Portuguese translation inside the _helios_server/locale_ directory. This
   can be used as a practical example of how to create one's own translation
   to one's own language of preference.

### Patching is very sensible to upstream
   The main problem with this patching approach is: since, for some reason,
   the original English text strings are dispersed everywhere in the code,
   every single time the original Helios code is modified upstream there is
   a major chance that the translation patch will be broken as well. And a
   broken translation patch will fail to fully tag the text strings. That
   makes it rather difficult to keep this effort in sync with upstream
   modifications (which currently do not seem to happen very often). That is
   the main reason why an _helios-server_ version is provided in here as well:
   it is a version of Helios that is known to be compatible with the
   translation patch and the instructions provided herein. The ideal situation
   is for the original Helios code to already provide the strings properly
   tagged for translation.
   

## Notes on translation

### General translation
   The default translation process is rather simple: simply edit the _msgstr_
   line in the template with a translation string that corresponds to the
   accompanying _msgid_ string. Example:

   ```console
   msgid "Original string"
   msgstr "My translation"
   ```

### Translating long strings
   Upon inspecting the django.po file you will notice that some of the strings
   in there are quite long. If your translation string needs to span multiple
   lines then you will need to make a **multiline translation**. A multiline
   translation leaves the _msgstr_ line with an empty string and fills the
   translation in the following lines below until it is done, making sure
   every line is a single string. The Django translation mechanism then takes
   care of joining those lines in a single string.

   An example will make it clear:

   ```console
   msgid ""
   "This is a somewhat long string that spans multiple lines. I will most "
   "probably need to translate this into the language of my preference by "
   "using a multiline translation."
   msgstr ""
   "Here goes the translation of the above string into the language of my "
   "preference. Notice that the 'msgstr' line contains an empty string and "
   "also notice that every line is a separate string of its own."
   ```

   In principle there is abolutely no compatibility problem if the original
   string is short and your translation is long (do try to keep it somewhat
   compatible in size, though; this avoids problems with possibly poor GUI
   decisions or limitations):

   ```console
   msgid "A short string"
   msgstr ""
   "For some reason the short string above translates to something quite big "
   "in the language of my preference. That is not a problem: all I have to do "
   "is provide a multiline translation."
   ```

   Finally, do notice that each string in a multiline translation ends with a
   space character. Remember that Django will join all the single strings into
   one big string in the end: without the space, the final word in a line string
   will be concatenated to the word in the beginning of the following line. As
   an alternative, instead of ending each string with a space one may opt to
   **start** each string with a space: the final result will be the same. Just
   make sure words are not concatenated senselessly to each other.
   

### Strings with formatting
   Some strings inside the django.po file will contain formatting information,
   such as new lines or tags as `<li>`, `<b>`, `<u>` etc. For such strings,
   try to keep the same formatting (or as close as possible) in the translation
   you provide so as to keep the same visual as what is seen by the user in the
   default English language. Examples may be found in the Brazilian Portuguese
   version provided here.


### String contexts
   Some strings might be accompanied of an additional attribute by the name
   of _msgctxt_. This specifies the **context** in which the string is used
   in the code. It is an attempt to give the translator hints on what the
   string being translated is referring to. That being said, some of the
   contextual markers might not make sense in every language. As an example,
   some languages, like Brazilian Portuguese, need to flex adjectives
   according to gender, which is what the "masculine" and "feminine" contexts
   try to establish. In languages that do not flex words according to gender,
   this contextual marker makes no sense.

### Static files and redirections
   As previously mentioned, static files in the code tree are not touched by
   any translation machinery. That way,an option to translate such files is
   to add a manually translated version to each language aimed to be supported:
   the correct version to be presented is then chosen by JavaScript redirection
   code which detects the browser's requested language.

   Below is a listing of all files in the code tree that need to be modified
   or created manually in order for translation to work for those files:

   * helios-server/helios/media/static_templates/[LANG]/
     - question.html

   * helios-server/helios/templates/email/[LANG]/
     - info_body.txt
     - info_subject.txt
     - result_body.txt
     - result_subject.txt
     - simple_body.txt
     - simple_subject.txt
     - vote_body.txt
     - vote_subject.txt

   * helios-server/heliosbooth/templates/[LANG]/
     - audit.html
     - done.html
     - election.html
     - footer.html
     - header.html
     - question.html
     - seal.html
     - submit.html

   * helios-server/server_ui/templates/email/[LANG]/
     - cast_vote_body.txt
     - cast_vote_subject.txt

   * helios-server/heliosbooth/
     - single-ballot-verify.html (**converted to redirection**)
     - vote.html (**converted to redirection**)
     - default-sbv.html (_sbv_ is short for _single-ballot-verify_)
     - default-vote.html
     - [LANG]-sbv.html
     - [LANG]-verifier.js
     - [LANG]-verifierworker.js
     - [LANG]-vote.html

   * helios-server/heliosverifier/
     - verify.html (**converted to redirection**)
     - default-verify.html
     - [LANG]-verify.html

   Directories and files with **[LANG]** mean those directories and files need
   to be created substituting **[LANG]** with the code for the language you are
   translating to (e.g. _pt-BR_ for Brazilian Portuguese or _nl_ for Dutch).
   Files marked with **converted to redirection** are files present in the
   original Helios code that have been fully modified with the sole purpose of
   redirecting execution to the correct language file; their original versions
   have been delegated to the similarly named _default-*_ variants.

   This is the currently ugly part of the translation process: all of the above
   files need to be manually verified for strings, each of which then need to
   be manually translated in place by a corresponding version in another
   language. Careful not to modify any code! Again, the Brazilian Portuguese
   version of such files (pt-BR) may be used as a template.

   **OBSERVATION 1:** the translation patch eases this process by taking care
   of creating the redirection files as well as the _default-*_ versions, so
   you will only need to create your [LANG]-* versions of those.

   **OBSERVATION 2:** do take care if your language code is composed of a
   language and country specification (e.g. pt-BR for Brazilian Portuguese).
   The Django translation in _helios-server/locale/_ expects a **locale name**:
   in the case of Brazilian Portuguese, that is **pt_BR** (language and
   country are separated by an underscore). In the case of the above
   static file translations, which use JavaScript, a **language code** is
   expected: in the case of Brazilian Portuguese, that is **pt-BR** (notice
   the dash separating language and country specifications).