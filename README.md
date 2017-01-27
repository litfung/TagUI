# TA.Gui
TA.Gui is a tool for non-developers and business users to automate web apps

![Sample Automation](https://github.com/tebelorg/TA.Gui/raw/master/sample.png)

# Why This
Automate repetitive parts of your work - use cases include data acquisition, process and test automations.

TA.Gui converts automation flows in simple natural language into lines of JavaScript code for CasperJS & PhantomJS to perform their web automation magic. For example, TA.Gui will instantly convert the flow below into 120+ lines of working JavaScript code and perform the series of steps to download a Typeform report automatically.

The flow can be triggered from scheduling, command line, REST API, URL, email etc. Everything happens headlessly in the background without seeing any web browser, so you can continue to use the computer or server uninterrupted. Running on a visible web browser is also supported, using Firefox and SlimerJS (see firefox option below).

```
https://www.typeform.com
click login
type username as user@gmail.com
type password as 12345678
click btnlogin
hover Test Event
click action results tooltip
click section_results
download https://admin.typeform.com/form/2592751/analyze/csv to report.csv
```
If you already know JavaScript coding and want to be more expressive, you can even use JavaScript directly in the flow. If not, you will still enjoy optional features like repositories to store your reusable objects, datatables for batch automation, and a Chrome extension in the pipeline which generates automation flows by learning from your actions.

# Set Up
1. install CasperJS (navigation/testing for PhantomJS) - http://casperjs.org
2. install PhantomJS (headless scriptable web browser) - http://phantomjs.org
3. install TA.Gui (insanely easy lightning fast automation tool) - https://git.io/vMCTZ

Optional - configure browser settings in tagui_config.txt or leave as default

# To Use
```
./tagui flow_filename option(s)
```
- Automation flow filename extension can be no extension, .gui or .txt
- Objects, keywords, datatables can be defined in flow_filename.csv (optional)

Option|Purpose
:----:|:------
firefox|run on visible Firefox browser (need to install Firefox, and SlimerJS - https://slimerjs.org)
report|generate a web report for easy sharing of run results (default is only text log file)
debug|show run-time backend messages from PhantomJS for detailed tracing and logging
test|professional testing using CasperJS [assertions](http://docs.casperjs.org/en/latest/modules/tester.html); support TA.Gui XPath tx('selector')

To schedule an automation flow in crontab, eg at 8am daily
```
0 8 * * * /full_path_on_your_server/tagui flow_filename option(s)
```

# Pipeline
Feature|Purpose
:-----:|:------
Chrome Extension|auto-generation of automation flows
One Package|auto-installing or packaging in dependencies
Microsoft Friendly|support running on Microsoft Windows
Enhancements|feel free to review and suggest new features

# Cheat Sheet
### STEPS
- [XPath method](http://www.w3schools.com/xml/xpath_intro.asp) is robust for identification and used to check for elements on webpage
- TA.Gui checks XPath automatically in following order full-xpath, id, name, class, title, text()

Step|Parameters (separator in bold)|Purpose
:---|:-----------------------------|:------
tap / click|element to click|click on an element
hover / move|element to hover|move cursor to element
type / enter|element to type ***as*** text to type|enter element as text
read / fetch|element to read ***to*** variable name|fetch text to variable
show / print|element to read |print element text to output
download|url to download ***to*** filename to save|download url to file
receive|url keyword to watch ***to*** filename to save|receive resource to file
echo|text (in quotation marks) and variables|print text/variables to output
save|element to read ***to*** optional filename|save text to file
dump|variable name ***to*** optional filename|save variable to file
snap|element (page = webpage) ***to*** optional filename|save screenshot to file
wait|optional time in seconds (default 5)|wait for some time
check|condition to check **&#124;** text if true **&#124;** text if false|check condition and print result
frame|frame name **&#124;** subframe name if any|next step in frame/subframe

### CONDITIONS
- Conditions can be expressed in natural language (brackets optional) or JavaScript
- JavaScript can be used (CasperJS's context); eg if/for/while applies to the next step

Condition (in natural language)|JavaScript
:------------------------------|:---------
for example - if A more than B and C not equal to D | if (A > B) && (C != D)
for example - while cupcakes equal to 12| while (cupcakes == 12)
more than or equal to / greater than or equal to / higher than or equal to|>=
less than or equal to / lesser than or equal to / lower than or equal to|<=
more than / greater than / higher than|>
less than / lesser than / lower than|<
not equal to|!=
equal to|==
and|&&
or|&#124;&#124;

### REPOSITORIES
- Repositories help to make steps or objects reusable and improve readability
- You can define custom repositories to store objects and keywords definitions
- Save repository file with same name as your flow filename and with .csv behind
- Repository must have 2 columns, for example below (headers up to you to name)
- Using \`object\` in your flow replaces it with its definition (which can contain objects)
- For example, \`type email\` becomes type user-email-textbox as user@gmail.com

OBJECT|DEFINITION
:-----|:---------
create account|btn btn--green btn-xl signup-btn
email|user-email-textbox
type email|type \`email\` as user@gmail.com

### DATATABLES
- Datatables extend the power of repositories to allow batch automation
- You can define datatables to store different datasets for batch automation
- Save datatable file with same name as your flow filename and with .csv behind
- Datatable has 2 or more columns, for example below (headers up to you to name)
- Data-centric approach with rows representing data fields (usually row = test case)
- TA.Gui loops through each column to automate using values from different datasets
- Eg, echo "TESTCASE - \`testname\`" in your flow shows TESTCASE - Trade USDSGD

TEST TRADES|TEST #1|TEST #2|TEST #3
:----------|:------|:------|:------
testname|Trade USDSGD|Trade USDJPY|Trade EURUSD
username|test_account|test_account|test_account
password|12345678|12345678|12345678
currency-pair|USDSGD|USDJPY|EURUSD
size|10000|1000|100000
direction|BUY|SELL|BUY

### REST API
Automation flows can be triggered via REST API. TA.Gui has an API service and runner for managing a queue of incoming requests via API. To setup, add a crontab entry on your server with the desired frequency to check and process incoming service requests. For example, the following job will check every hour and run pending flows in the queue.
```
0 * * * * /full_path_on_your_server/tagui_crontab
```

To call an automation flow from your application or web browser, use below API syntax. Automation flows can also be triggered from emails using the API. For email integration, install and set up [TA.Mail](https://github.com/tebelorg/TA.Mail) (mailbot to act on incoming emails).
```
your_website_url/tagui_service.php?SETTINGS="flow_filename option(s)"
```

API Related Files |Purpose
:-------------|:------
tagui_service.php|receiving service requests into queue
tagui_runner.php|retrieving service requests from queue
tagui_crontab|managing execution from crontab schedule
tagui_service.in|log to track incoming service requests
tagui_service.out|log to track processed service requests
tagui_service.log|log to track service requests history
tagui_service.act|service request batch ready to execute
tagui_service.run|service request batch currently running
tagui_service.done|service request batch finished running

# License
TA.Gui is open-source software released under the MIT license
