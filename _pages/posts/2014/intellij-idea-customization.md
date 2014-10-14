title: My IntelliJ IDEA customization
decorator: post
identifier: d7d18c169f4a051fdc2f
description: IntelliJ IDEA is a full-featured IDE. I turned to that since 2012, now I'm a big fans of JetBrains series. This post would share all my customization about IDEA.
‡‡‡‡‡‡‡‡‡‡‡‡‡‡

IntelliJ IDEA is a full-featured IDE with a high level of usability and outstanding advanced code editing and refactoring support. I turned to that since 2012, now I'm a big fans of `JetBrains` series. I'm enthusiasm for customizing my IDE. As some records for my development life. I wish to show here about all my settings.



# General Settings

As the most popular way, I applied the `Darcula` theme as well. I got used to the `Ubuntu` font when I developing on Ubuntu platform, thus I followed my habit.

![](/images/intellijidea/theme_font_setting.png)

Because I'm work in China, so I must make the IDE supports Chinese without problems. I prefer `Microsoft YaHei` and `Consolas`, so I finally found a font which mix them into one.

![](/images/intellijidea/editor_font.png)



# Keymap

Start from `Eclipse`, I enjoy to use shortcut key to speed up my developing. Now I absolutely broke away the Mouse in my job, only the Keyboard. The reason of why I can do that is I've been changed plenty key settings to fulfill my desires with the Keyboard rather than the Cursor. Eventually I almost own all the frequently-used keys.

| Shortcut Key | Understanding |
| :----------: | ------------- |
| Alt+Shift+Z | `Code > Generate...` : Invoking the Generate menu. This quick menu is helpful, I often use it to generate Getter and Setter, Override parent's methods.<br>![](/images/intellijidea/code_generate.png) |
| Ctrl+Shift+F | `Code > Reformat Code...` : Reformat the caret pointing to file code.<br>![](/images/intellijidea/reformat_code.png)|
| Ctrl+Shift+O | `Code > Optimize Imports...` : An unnecessary imports would never be a good thing. Especially for a person who pursue of perfect like me. Always keep your imports optimized and rearrange those statements.<br>e.g. before is :<br>![](/images/intellijidea/optimize_imports_before.png)<br>after optimize :<br>![](/images/intellijidea/optimize_imports_after.png) |
| Alt+Up/Down | `Code > Move Statement Up/Down` : Swapping the caret laying on code block by block. e.g. swap of **while** block and **if** block, before is :<br>![](/images/intellijidea/move_statement_before.png)<br>After press `Alt+Up` :<br>![](/images/intellijidea/move_statement_after.png) |
| Ctrl+Shift+. | `Code > Folding > Fold Selection/Remove region` : Expand/collapse the code block. Can be beneficial while checking a big file.<br>![](/images/intellijidea/fold_selection.png) |
| Ctrl+Shift+Down | `Edit > Duplicate Lines` : Duplicating the caret laying on line or selecting lines at below. e.g. duplicate an **if** block, before is :<br>![](/images/intellijidea/duplicate_lines_before.png)<br>After performed :<br>![](/images/intellijidea/duplicate_lines_after.png) |
| Ctrl+Shift+U | `Edit > Toggle Case` : Toggle between uppercase and lowercase for the caret laying on code block or selecting text. |
| Ctrl+Shift+C | `Edit > Copy Paths` : Copy the caret pointing to resource's absolute path to the Clipboard. |
| Ctrl+Shift+V | `Edit > Paste from History...` : Display a dialog listed your recent copy string, select one to synchronize to the Clipboard and paste at caret.<br>![](/images/intellijidea/paste_from_history.png) |
| Ctrl+,(Comma) | `Edit > Find Previous` : Invoking the finding text view in editor, or navigating the caret to the previous match result after search.<br>![](/images/intellijidea/editor_find_previous.png) |
| Ctrl+.(Period) | `Edit > Find Next` : Invoking the finding text view in editor, or navigating the caret to the next match result after search.<br>![](/images/intellijidea/editor_find_next.png) |
| Alt+Shift+Left/Right | `Edit > Move caret to code block start/end with selection` : Forward or Backward to select code block.<br>![](/images/intellijidea/block_selection.png) |
| Ctrl+Shift+L | `Edit > Select Word at Caret` : Select the independent word at caret. |
| Ctrl+M | `Edit > Scroll to Center` : Scroll caret line to center of editor. |
| Ctrl+Shift+R | `Navigate > File...` : Navigating to a file in project by name.<br>![](/images/intellijidea/navigate_file.png) |
| Ctrl+O | `Navigate > File Structure` : Show us that the IDE recognized file structure.<br>![](/images/intellijidea/file_structure.png)![](/images/intellijidea/file_structure_1.png) |
| Ctrl+K | `Navigate > Implementation(s)` : Navigating to the caret laying on method's implementation, display a choose dialog if more.<br>![](/images/intellijidea/implementations.png) |
| F3 | `Navigate > Declaration` : Just navigating to the place where the object declaring. Maybe an Interface method means take not valueable code. So I use `Ctrl+K` all the time. |
| Ctrl+Shift+J/K |`Navigate > Previous/Next Highlighted Error` : Navigating the caret to previous or next reported error or warning.<br>![](/images/intellijidea/highlighted_error.png) |
| Alt+Shift+H | `Navigate > Select In...` : Select a Target to open the caret laying on file.<br>![](/images/intellijidea/select_in_target.png) |
| Ctrl+Q | `Navigate > Last Edit Location` : Navigating the caret to the last edit location. |
| F2 | `Refactor > Rename...` Renaming correspond.<br>![](/images/intellijidea/refactor_rename.png) |
| Alt+Shift+V | `Refactor > Move...` : In Project explorer, move file to another place.<br>![](/images/intellijidea/move_file.png) |
| Alt+Enter | `Other > Show Intention Actions` : Showing the caret laying on object's intentions. Sometime are error solutions :<br>![](/images/intellijidea/show_intention_actions.png)Sometime are optimize advices :<br>![](/images/intellijidea/show_intention_actions_1.png)I'm used to pay attention for these tips because I trust IDEA's wisdom for code, they're capable to make my work better. |
| Ctrl+N |`Other > Show Context Menu` : Showing the caret pointing to resource's menu. Equivalent to use the right mouse button invoke's menu.<br>![](/images/intellijidea/show_context_menu.png) |
| Ctrl+Shift+A | `Help > Find Actions` : Typing the action name to perform, you can invoke any action if you have remembered his name.<br>![](/images/intellijidea/find_actions.png) |
| Ctrl+J | `Code Completion > Basic` : Default key is `Ctrl+Space`. But it's conflict with System's switch input method key. This customization was inherited since I use Visual Studio 2008 to study `.Net`. It's within easy reach while we typing with two hands.<br>![](/images/intellijidea/code_completion_basic.png) |
| Shift+Alt+Space | `Code Completion > SmartType` : Relative to the **Basic**, this completion will estimate the expression which you just write or the caret laying on, finally supply us some smart suggests.<br>![](/images/intellijidea/code_completion_smartype.png) |
| Alt+Shift+X | `Run > Run` : Running the configuration scheme which the last time has been ran. |
| Alt+Shift+F10 | `Run > Run...` : Choosing a configuration scheme to run.<br>![](/images/intellijidea/run_run.png) |
| Alt+Shift+F9 | `Run > Debug...` : Choosing a configuration scheme to debug.<br>![](/images/intellijidea/run_debug.png) |
| Ctrl+Shift+B | `Run > Toggle Line Breakpoint...` Handy way to switch the Breakpoint of lines.<br>![](/images/intellijidea/toggle_line_breakpoint.png) |
| Ctrl+Shift+F8 | `Run > View Breakpoints...` : Managing all Breakpoints in project.<br>![](/images/intellijidea/managing_breakpoints.png)Or easily checking the caret laying on Breakpoint's detail :<br>![](/images/intellijidea/managing_breakpoints_1.png) |
| Alt+Shift+Y | `View > Compare with Clipboard` : Compare the caret laying on file or selecting text with Clipboard.<br>![](/images/intellijidea/compare_with_clipboard.png) |
| Alt+Shift+J | `View > Parameter Info` : Checking the method's parameter info.<br>![](/images/intellijidea/parameter_info.png) |
| Alt+Shift+U | `View > Quick Documentation` : Invoking the object(class, method, argument, parameter)'s documentation which the caret standing on.<br>![](/images/intellijidea/quick_documentation.png) |
| Ctrl+E | `View > Recent Files` : Show recent files operations.<br>![](/images/intellijidea/recent_files.png) |
| Ctrl+Shift+I | `View > Quick Definition` : View the caret laying on object's definition.<br>![](/images/intellijidea/quick_definition.png) |
| Alt+1 | `Tool Windows > Project` : Open the Project window.<br>![](/images/intellijidea/project_window.png) |
| Alt+2 | `Tool Windows > Android` : Open the Android DDMS window.<br>![](/images/intellijidea/android_window.png) |
| Alt+3 | `Tool Windows > Changes` : Open the Changes(Local/Log) window.<br>![](/images/intellijidea/changes_window.png) |
| Ctrl+Shift+D | `Window > Hide All Tool Windows` : Hide all tool windows, maximize the editor. |
| Ctrl+W | `Window > Close Tab` : Close a Tab in editor or hide a window in Tools. |
| Ctrl+Shift+W | `Window > Close All Tab` : Close All Tab in editor. |
| Ctrl+Shift+N/M | `Window > Select Previous/Next Tab` : Switch the tabs circularly. |
| Ctrl+Shift+Y | `File > New...` : Showing the new file dialog. Once perform in the editor, means creating a file in the same directory for the editing file.<br>![](/images/intellijidea/file_new.png) |
| psvm+Tab | `Live Templates` : Input "psvm" then press `Tab`, IDEA will generate the java main method, look at the Live Templates for more patterns.<br>![](/images/intellijidea/live_templates.png) |
| Ctrl+Shift+Z | `VCS > Show History` : Show the caret laying on object's history.<br>![](/images/intellijidea/vcs_history.png) |
| Ctrl+Shift+X | `VCS > Compare with the Same Repository Version`<br>![](/images/intellijidea/vcs_compare_same_version.png)|




# System Setting

I switch from Windows-style keyboard to Mac OS X. I already got used to the `Control` key always separated by another key(normally is Windows key). So I did a small remap for my Mac keyboard, swapped the `Option(Alt)` key and the `Command` key.

![](/images/intellijidea/swap_option_and_command_key.png)



# Additional

In the age of **Eclipse**, we can only compressing the workspace's `.metadata` folder then keep that package to backup our settings. It's usually being large file and harder to sharing to others.

Thanks for JetBrains, enable us to holding our entire settings through a small size file. [Here](/intellij_idea_settings.jar)'s all settings of mine that exported from my IDEA. Just import into your IDE if you wants to share my customization.
























