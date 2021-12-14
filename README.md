[Open](https://www.icloud.com/shortcuts/00940761fcdc4d7eb5b2bf77a95a6080) is a shortcut for the iOS Shortcuts app, which opens links and files into specified apps, as defined in a `json` dictionary.

## Usage
To open files and URLs in the specified apps, simply invoke the shortcut in Share Sheet. Here are some examples:
- I have specified `opml` to open in [OmniOutliner](https://apps.apple.com/gb/app/omnioutliner-3-enterprise/id1484581472) in the shortcut. When I share an `opml` file into the shortcut, the file is automatically opened in OmniOutliner. 
- Furthermore, I specified a *list* of apps to open PDF documents. When opening a PDF document in the Share Sheet, the shortcut presents this list presents this list, and opens the document in the chosen app. 
- For URLs to downloadable files, the files are downloaded and shared instead of the URLs. For example, this can be used to open a `txt` file on a server directly into [Textastic](https://apps.apple.com/gb/app/textastic-code-editor-9/id1049254261).
- For URLs, I have built in the URL schemes of the Twitter client [Twitterrific](https://apps.apple.com/gb/app/twitterrific-tweet-your-way/id580311103). The shortcut automatically detects Twitter URLs and directs it into the corresponding tabs in Twitterrific[^When sharing URLs from Safari (Safari webpage as input), I have made default for the shortcut to open in apps without further interaction. But when sharing from other apps (URL as input), the shortcut asks whether to open in the default browser or the specified app. Otherwise, it would have not been possible to route the URLs from the specified apps to the default browser, since the shortcut would still try to open in the specified apps. The user interaction is necessary as the Shortcuts app has no way to detect the host app.].
- Besides the built-in URL schemes for [Twitterrific](https://apps.apple.com/gb/app/twitterrific-tweet-your-way/id580311103) (Twitter), [Apollo](https://apps.apple.com/gb/app/apollo-for-reddit/id979274575) (Reddit), [Octal](https://apps.apple.com/gb/app/octal-for-hacker-news/id1308885491) (Hacker News) and [V](https://apps.apple.com/gb/app/v-for-wikipedia/id993435362) (Wikipedia), we can make “add-ons” to direct any hostname to any apps with a URL scheme.

## Limitation
There are some limitations for this shortcut. The obvious one is that it requires an extra step of invoking the Share Sheet. This is more like always using “Open with…” on macOS. Another important limitation to keep in mind is that files opened via the shortcut is not opened in place. The files opened are copies of the original. This is because the Shortcuts app always caches the input from Share Sheet, rather than passing the original files[^The only way to pass the original file in the Shortcuts app is to use the “Select File” action.].

## Logic
The shortcut can be divided into three parts:
- classification of input types,
- assignment of options to open the input with,
- process the chosen opening option.
### Classification
The first part of the shortcut tries to classify the input by types. These types are used to define target apps to open the inputs. 

Initially it assigns the variable `Type` according to the “Get Type” action. Then it tries to extract URLs from single-line `Text` inputs, and re-classify `Safari web page` as `URL`. The next step is to determine distinguish `Non-Standard URL` schemes from `HTTP URL`, and determine if `HTTP URL` is `Remote File URL` leading to a remote file. The non-file HTTP URLs are further classified into `Twitter URL`, `Reddit URL`, `Hacker News URL`, and `Wikipedia URL`. The files from remote file URLs are downloaded and re-classified according to their extension. Other HTTP URLs are assigned the `Type` of `HTTP URL @hostname`.

### Assignment
The second part of the shortcut assigns a list of opening options to the input according to the `Type`, by looking at the dictionary from the `open-config.json`. 

When sharing from Safari, the options to open in default browser and Quick Look are removed to reduce user interaction.

If the final `Options` is empty, then the shortcuts falls back to Quick Look.

If `Options` contain more than one items, then it prompts to choose one option from the list. 

### Processing
The last stage of the shortcut processes the input and opens it in the chosen target app. 

To open URLs in apps, the URL schemes of the apps are required. It is unfeasible to include the URL schemes of all apps. I have coded in the ones of Twitterrific, Apollo, Octal, and V. Other schemes can be easily added via add-on shortcuts. For example, we can make a shortcut ”Open in YouTube” that replaces `https://` with `youtube://` in inputs and opens the replaced URLs. The main “Open” shortcut can route all `https://www.youtube.com` URLs by adding the line `”HTTP URL @www.youtube.com”: [“Open in YouTube”]` in the `open-config.json`.

To open files in apps, the shortcut uses the string following “Open in ” in the open `Option` as the app name to open the input in.
## Customisation
The shortcut has a default configuration:
<pre><code>
{
	“URL”: [“Open in Browser”, “Quick Look”],
	“HTTP URL”: [“Open in Browser”, “Quick Look”],
	“Non-Standard URL”: [“Open in Browser”, “Quick Look”],
	“Twitter URL”: [“Open in Twitterrific”, “Open in Browser”, “Quick Look”],
	“Reddit URL”: [“Open in Apollo”, “Open in Browser”, “Quick Look”],
	“Wikipedia URL”: [“Open in V”, “Open in Browser”, “Quick Look”],
	“Hacker News URL”: [“Open in Octal”, “Open in Browser”, “Quick Look”],
	“Text”: [“Open in Textastic”, “Quick Look”],
	“txt”: [“Open in Textastic”, “Quick Look”],
	“md”: [“Open in iA Writer”, “Open in Textastic”, “Quick Look”],
	“PDF”: [“Open in GoodReader”, “Open in GoodNotes”, “Open in Prizmo”, “Open in PDFGenius”, “Open in Badger”, “Quick Look”],
	“ooutline”: [“Open in OmniOutliner”],
	“opml”: [“Open in OmniOutliner”],
	“eml”: [“Open in EML Viewer”],
	“apkg”: [“Open in Anki”],
	“colpkg”: [“Open in Anki”],
	“hc”: [“Open in Disk Decipher”],
}
</code></pre>

The shortcut can be customised by providing a `open-config.json` file with the same format in the “Shortcuts” folder on “iCloud Drive”. Here are some examples:
- To change the default text editor to open “Text” files without extensions, replace `Textastic` in the line `”Text”: [“Open in Textastic”, “Quick Look”],` with the name of your chosen text editor. Furthermore, you can remove `, “Quick Look”` to open in the text editor directly without selecting in the menu.
- To open YouTube links in the [YouTube](https://apps.apple.com/gb/app/youtube-watch-listen-stream/id544007664) app, add the line `”HTTP URL @www.youtube.com”: [“Open in YouTube”]`, and add the shortcut [Open in YouTube](https://www.icloud.com/shortcuts/7ae11ed886174a8a928f50f22e50e8c3).