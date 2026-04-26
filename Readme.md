---
name: Bug report
about: Create a report to help us improve
title: ''
labels: ''
assignees: ''

---

**Describe the bug**
A clear and concise description of what the bug is.

**To Reproduce**
Steps to reproduce the behavior:
1. Go to '...'
2. Click on '....'
3. Scroll down to '....'
4. See error

**Expected behavior**
A clear and concise description of what you expected to happen.

**Screenshots**
If applicable, add screenshots to help explain your problem.

**Desktop (please complete the following information):**
 - OS: [e.g. iOS]
 - Browser [e.g. chrome, safari]
 - Version [e.g. 22]

**Smartphone (please complete the following information):**
 - Device: [e.g. iPhone6]
 - OS: [e.g. iOS8.1]
 - Browser [e.g. stock browser, safari]
 - Version [e.g. 22]

**Additional context**
Add any other context about the problem here.
# gitignore template for ecu.test workspaces - by tracetronic https://tracetronic.com
# website: https://www.ecu-test.com
#   * all directories are related to the default directories, please adapt the .gitignore if you use customized directories

# Dynamic workspace settings
#   * We don't recommend to ignore the .workspace directory, because of important 
#     * project specific settings
#     * local user settings
.workspace/ETdrive.xml
.workspace/favorites.xml
.workspace/filters.xml
.workspace/generators.xml
.workspace/history.xml
.workspace/parallelExecution.xml
.workspace/signalviewer.xml
.workspace/signalViewerHistory.json
.workspace/signalviewer2layout.xml
.workspace/testeditor.xml
.workspace/tooladapter.xml
.workspace/view.xml
# optional, if your process depends on this file remove exclusion
.workspace/attributeLists.xml
.workspace/interactiveexecution.xml
.workspace/protocol.xml
.workspace/pythonlibrary.xml
# deprecated, support for older versions
.workspace/traceexplorer.xml

# Custom file formats and test dependencies
#  * you can manage your artifacts also with test.guide (https://www.test-guide.info) and reference them via Playbooks
*.arxml
*.a2l
*.dbc
*.hex
*.s19
[tT]estdata
[tT]estdaten

# Test results and test execution related content
#   * Git is not intended to store and provide test results for all iterations
#   * We recommend to use test.guide (https://www.test-guide.info) for the test report management
TestReports

# Report generators and templates
#   * if you want to provide (f.e.) your own report generators exclude the directory here and ignore only the unnecessary subdirectories
Templates

# optional, default for external Python libraries
PyLibs

# Exclude large binary artifacts
#  * you can manage your artifacts also with test.guide (https://www.test-guide.info) and reference them via Playbooks
Offline-FIUs
Offline-Models
Offline-SGBDs
*.exe
*.msi
*.zip
*.7z

# Exclude default and custom temporary directories
Backup_*

# Python bytecode and cache files
__pycache__/
*.py[cod]
