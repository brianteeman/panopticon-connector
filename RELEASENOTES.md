## ℹ️ What is this?

This is the connector component for [Akeeba Panopticon](https://github.com/akeeba/panopticon), our self-hosted site monitoring software. You need to install it on your site to be able to monitor it with Akeeba Panopticon.

ℹ️ If you have a Joomla! 3 site please [look at the Joomla 3 connector's repository](https://github.com/akeeba/panopticon_connector_j3/releases/latest) instead.

ℹ️ If you have a WordPress site please [look at the WordPress connector's repository](https://github.com/akeeba/panopticon-connector-wordpress/releases/latest) instead.

## 🔎 Release highlights

* **🐞 Package downloads failed on Joomla! 5.4 and later**. Every download the connector performs — the Joomla! core update package, its update details XML, and extension packages installed from a URL — failed on Joomla! 5.4 and later, including all Joomla! 6 versions. `Joomla\Http\Response` stopped populating its legacy public `$code` and `$body` properties, which read back as `null`; the connector's status check turned that into a `0` and rejected every successful download with a misleading “Unable to download the package file. (HTTP 400)” error. The connector now selects the correct accessor for the Joomla! version it is running on, so this keeps working on Joomla! 4.0 through 6.x. If Panopticon has been unable to update the core or install extensions on your newer sites, this is why.

* **🐞 Core updates failed on Joomla! 5.1–5.2**. A PHP fatal error (`Call to undefined method Joomla\Filesystem\File::exists()`) caused the `core/update/activate` API endpoint to return HTTP 500 on sites running Joomla! 5.1.x or 5.2.x, blocking all Panopticon-initiated core updates on those versions. The method was removed from the `joomla/filesystem` library bundled with Joomla! 5.0–5.2 and only restored in 5.3. The connector now uses the equivalent native PHP call, which works across all supported Joomla! versions.

* **🐞 Akeeba Backup integration**. Two separate problems are fixed. Enabling the “Web Services - Akeeba Backup” plugin wrote to the wrong database column, which renamed the plugin instead of publishing it and left the Akeeba Backup JSON API v3 unreachable. Separately, failing to read or provision the Akeeba Backup Secret Word aborted the entire Akeeba Backup information request; the connector now reports the missing Secret Word and carries on, so sites which authenticate with a Joomla! API Token are no longer affected. **If your site's “Web Services - Akeeba Backup” plugin was renamed by an earlier version of the connector, check its name in Joomla!'s Plugins page and correct it.**

* **✨ Translations**. Machine translations are now bundled for German, Greek, Spanish, French, Italian, and Portuguese. These are machine-generated and have not been reviewed by a human translator. Corrections are welcome.

* **✨ Custom Core File Integrity checksums source**. You can now point the Core File Integrity check at a different base URL for its checksums. This is meant for sites which cannot reach the default source, and for people who mirror it internally.

## 🖥️ System Requirements

* Joomla! 4.0 to 6.2, inclusive.
* PHP versions 7.2 to 8.6, inclusive.

**Important notes on system requirements**

PHP version compatibility refers to our connector, not Joomla! itself. PHP 8.1 or later required for Joomla! 5, PHP 8.3 or later required for Joomla! 6.

This version will refuse to install on a PHP or Joomla! version outside the range above, telling you which version it found. Previous versions installed on anything and failed later in ways which looked like a bug in the connector. If you need to run a newer version than we list, wait for the connector release which supports it.

Future versions of the connector will drop support for Joomla 5.3 and earlier versions. We strongly advise you to upgrade to Joomla! 5.4 or 6.x as soon as possible.

## 🧑🏽‍💻 Important note about the Joomla! API

Akeeba Panopticon Connector uses the Joomla! API application (the `/api` folder on your site). Around February 2023 there was a lot of unnecessary panic, leading some people to disable access to this folder through their `.htaccess` file. If you did that, you need to undo that change to re-enable access to the `/api` folder.

When Akeeba Panopticon connects to sites running on Joomla! 4 and later it makes use of code provided not only by its own connector, but also core Joomla! plugins in the `webservices` and `api-authentication` folders. Please make sure that the following plugins are enabled on your site:

* `Web Services - Panopticon` (provided by this connector).
* `Web Services - Installer` (provided by Joomla!).
* `API Authentication - Web Services Joomla Token` (required for secure, token-based authentication to the Joomla! API).

If any of these plugins are disabled, _or if its Access is set to anything other than Public_, you will run into connection problems with your site. The connector itself will try to detect and report these issues, but there are cases it might fail to identify the problem.

## 📋 CHANGELOG

* ✨ Add machine translations for `de-DE`, `el-GR`, `es-ES`, `fr-FR`, `it-IT`, and `pt-PT`
* ✨ Support a custom base URL for the Core File Integrity checksums source
* ✨ Support for PHP 8.6 and Joomla! 6.2
* ✏️ Installation is now refused on PHP and Joomla! versions outside the supported range
* 🐞 Package downloads failed on Joomla! 5.4 and later, including Joomla! 6, reporting a misleading “HTTP 400” error [#28]
* 🐞 Core updates fail with HTTP 500 on Joomla 5.1–5.2 due to removed `File::exists()` method [#26]
* 🐞 System information reported the CPU load averages incorrectly (empty 5-minute value, wrong 15-minute value)
* 🐞 System information never reported Linux CPU usage from `/proc/stat`
* 🐞 Editing an update site through the API failed with HTTP 500
* 🐞 Remote extension installation from a URL failed with HTTP 500 on PHP 8+ (non-static call to `HttpFactory::getHttp()`) [#27]
* 🐞 Enabling the “Web Services - Akeeba Backup” plugin wrote to the wrong column
* 🐞 Akeeba Backup Secret Word provision failure made the whole Akeeba Backup information request fail

Legend:
* 🚨 Security update
* ‼️ Important change
* ✨ New feature
* ✂️ Removed feature
* ✏️ Miscellaneous change
* 🐞 Bug fix
