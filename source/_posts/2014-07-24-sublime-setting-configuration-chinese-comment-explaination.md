---
layout: post
title: 'Sublime setting 設置文件中文解說'
date: 2014-07-24 07:31
comments: true
categories: 
---
###本篇來自這裡 [Sublime Text 2 設置文件詳解](http://my.oschina.net/Twitter/blog/139492) ，作者是中國的 IT開發者: 金三胖 
>>做個備份。 無侵權之用意。

 看來想駕馭好這軟件不弄清楚配置文件是不行了，周末找了時間把配置文件的每條配置信息都加上了中文注釋，現在貼出來和大家共享，裏面有解釋不清楚的也歡迎大家夥來互相討論:)

另外，這也是我的第一篇博客，以後我會多寫些前端方面的文章和大家共享，歡迎一起討論:)

Preferences.sublime-settings文件：

// While you can edit this file, it’s best to put your changes in
// “User/Preferences.sublime-settings”, which overrides the settings in here.
//
// Settings may also be placed in file type specific options files, for
// example, in Packages/Python/Python.sublime-settings for python files.
{
// Sets the colors used within the text area
// 主題文件的路徑
“color_scheme”: “Packages/Color Scheme – Default/Monokai.tmTheme”,

// Note that the font_face and font_size are overriden in the platform
// specific settings file, for example, “Preferences (Linux).sublime-settings”.
// Because of this, setting them here will have no effect: you must set them
// in your User File Preferences.
// 設置字體和大小，必須在Settings-User裏重寫，這裏設置沒有任何效果
“font_face”: “Consolas”,
“font_size”: 12,

// Valid options are “no_bold”, “no_italic”, “no_antialias”, “gray_antialias”,
// “subpixel_antialias” and “no_round” (OS X only)
// 字體選項：no_bold不顯示粗體字，no_italic不顯示斜體字，no_antialias和no_antialias關閉反鋸齒
// subpixel_antialias和no_round是OS X系統獨有的
“font_options”: [],

// Characters that are considered to separate words
// 在文字上雙擊會全選當前的內容，如果裏面出現以下字符，就會被截斷
“word_separators”: “./\\()\”‘-:,.;<>~!@#$%^&*|+=[]{}`~?”,

// Set to false to prevent line numbers being drawn in the gutter
// 是否顯示行號
“line_numbers”: true,

// Set to false to hide the gutter altogether
// 是否顯示行號邊欄
“gutter”: true,

// Spacing between the gutter and the text
// 行號邊欄和文字的間距
“margin”: 4,

// Fold buttons are the triangles shown in the gutter to fold regions of text
// 是否顯示代碼折叠按鈕
“fold_buttons”: true,

// Hides the fold buttons unless the mouse is over the gutter
// 不管鼠標在不在行號邊欄，代碼折叠按鈕一直顯示
“fade_fold_buttons”: true,

// Columns in which to display vertical rulers
//列顯示垂直標尺，在中括號裏填入數字，寬度按字符計算
“rulers”: [],

// Set to true to turn spell checking on by default
// 是否打開拼寫檢查
“spell_check”: false,

// The number of spaces a tab is considered equal to
// Tab鍵制表符寬度
“tab_size”: 4,

// Set to true to insert spaces when tab is pressed
// 設为true時，縮進和遇到Tab鍵時使用空格替代
“translate_tabs_to_spaces”: false,

// If translate_tabs_to_spaces is true, use_tab_stops will make tab and
// backspace insert/delete up to the next tabstop
// translate_tabs_to_spaces設置为true，Tab和Backspace的刪除/插入作用於制表符寬度
// 否則作用於單個空格
“use_tab_stops”: true,

// Set to false to disable detection of tabs vs. spaces on load
// false時禁止在載入的時候檢測制表符和空格
“detect_indentation”: true,

// Calculates indentation automatically when pressing enter
// 按回車時，自動與制表位對齊
“auto_indent”: true,

// Makes auto indent a little smarter, e.g., by indenting the next line
// after an if statement in C. Requires auto_indent to be enabled.
//針對C語言的
“smart_indent”: false,

// Adds whitespace up to the first open bracket when indenting. Requires
// auto_indent to be enabled.
// 需要启用auto_indent，第一次打開括號縮進時插入空格？（沒測試出來效果…）
“indent_to_bracket”: true,

// Trims white space added by auto_indent when moving the caret off the
// line.
// 顯示對齊的白線是否根據回車、tab等操作自動填補
“trim_automatic_white_space”: true,

// Disables horizontal scrolling if enabled.
// May be set to true, false, or “auto”, where it will be disabled for
// source code, and otherwise enabled.
// 是否自動換行，如果選auto，需要加雙引號
“word_wrap”: false,

// Set to a value other than 0 to force wrapping at that column rather than the
// window width
// 設置窗口內文字區域的寬度
“wrap_width”: 0,

// Set to false to prevent word wrapped lines from being indented to the same
// level
// 防止被縮進到同一級的字換行
“indent_subsequent_lines”: true,

// Draws text centered in the window rather than left aligned
// 如果沒有定義過，則文件居中顯示（比如新建的文件）
“draw_centered”: false,

// Controls auto pairing of quotes, brackets etc
// 自動匹配引號，括號等
“auto_match_enabled”: true,

// Word list to use for spell checking
// 拼寫檢查的單詞列表路徑
“dictionary”: “Packages/Language – English/en_US.dic”,

// Set to true to draw a border around the visible rectangle on the minimap.
// The color of the border will be determined by the “minimapBorder” key in
// the color scheme
// 代碼地圖的可視區域部分是否加上邊框，邊框的顏色可在配色方案上加入minimapBorder鍵
“draw_minimap_border”: false,

// If enabled, will highlight any line with a caret
// 突出顯示當前光標所在的行
“highlight_line”: false,

// Valid values are “smooth”, “phase”, “blink”, “wide” and “solid”.
// 設置光標閃動方式
“caret_style”: “smooth”,

// Set to false to disable underlining the brackets surrounding the caret
// 是否特殊顯示當前光標所在的括號、代碼頭尾閉合標記
“match_brackets”: true,

// Set to false if you’d rather only highlight the brackets when the caret is
// next to one
// 設为false時，只有光標在括號或頭尾閉合標記的兩端時，match_brackets才生效
“match_brackets_content”: true,

// Set to false to not highlight square brackets. This only takes effect if
// match_brackets is true
// 是否突出顯示圓括號，match_brackets为true生效
“match_brackets_square”: false,

// Set to false to not highlight curly brackets. This only takes effect if
// match_brackets is true
// 是否突出顯示大括號，match_brackets为true生效
“match_brackets_braces”: false,

// Set to false to not highlight angle brackets. This only takes effect if
// match_brackets is true
// 是否突出顯示尖括號，match_brackets为true生效
“match_brackets_angle”: false,

// Enable visualization of the matching tag in HTML and XML
// html和xml下突出顯示光標所在標簽的兩端，影響HTML、XML、CSS等
“match_tags”: true,

// Highlights other occurrences of the currently selected text
// 全文突出顯示和當前選中字符相同的字符
“match_selection”: true,

// Additional spacing at the top of each line, in pixels
// 設置每一行到頂部，以像素为單位的間距，效果相當於行距
“line_padding_top”: 1,

// Additional spacing at the bottom of each line, in pixels
// 設置每一行到底部，以像素为單位的間距，效果相當於行距
“line_padding_bottom”: 1,

// Set to false to disable scrolling past the end of the buffer.
// On OS X, this value is overridden in the platform specific settings, so
// you’ll need to place this line in your user settings to override it.
// 設置为false時，滾動到文本的最下方時，沒有緩沖區
“scroll_past_end”: true,

// This controls what happens when pressing up or down when on the first
// or last line.
// On OS X, this value is overridden in the platform specific settings, so
// you’ll need to place this line in your user settings to override it.
// 控制向上或向下到第一行或最後一行時發生什麼（沒明白也沒試出來）
“move_to_limit_on_up_down”: false,

// Set to “none” to turn off drawing white space, “selection” to draw only the
// white space within the selection, and “all” to draw all white space
// 按space或tab時，實際會產生白色的點（一個空格一個點）或白色的橫線（tab_size設置的制表符的寬度），選中狀態下才能看到
// 設置为none時，什麼情況下都不顯示這些點和線
// 設置为selection時，只顯示選中狀態下的點和線
// 設置为all時，則一直顯示
“draw_white_space”: “selection”,

// Set to false to turn off the indentation guides.
// The color and width of the indent guides may be customized by editing
// the corresponding .tmTheme file, and specifying the colors “guide”,
// “activeGuide” and “stackGuide”
// 制表位的對齊白線是否顯示，顏色可在主題文件裏設置（guide，activeGuide，stackGuide）
“draw_indent_guides”: true,

// Controls how the indent guides are drawn, valid options are
// “draw_normal” and “draw_active”. draw_active will draw the indent
// guides containing the caret in a different color.
// 制表位的對齊白線，draw_normal为一直顯示，draw_active为只顯示當前光標所在的代碼控制域
“indent_guide_options”: ["draw_normal"],

// Set to true to removing trailing white space on save
// 为true時，保存文件時會刪除每行結束後多餘的空格
“trim_trailing_white_space_on_save”: false,

// Set to true to ensure the last line of the file ends in a newline
// character when saving
// 为true時，保存文件時光標會在文件的最後向下換一行
“ensure_newline_at_eof_on_save”: false,

// Set to true to automatically save files when switching to a different file
// or application
// 切換到其它文件標簽或點擊其它非本軟件區域，文件自動保存
“save_on_focus_lost”: false,

// The encoding to use when the encoding can’t be determined automatically.
// ASCII, UTF-8 and UTF-16 encodings will be automatically detected.
// 編碼時不能自動檢測編碼時，將自動檢測ASCII, UTF-8 和 UTF-16
“fallback_encoding”: “Western (Windows 1252)”,

// Encoding used when saving new files, and files opened with an undefined
// encoding (e.g., plain ascii files). If a file is opened with a specific
// encoding (either detected or given explicitly), this setting will be
// ignored, and the file will be saved with the encoding it was opened
// with.
// 默認編碼格式
“default_encoding”: “UTF-8″,

// Files containing null bytes are opened as hexadecimal by default
// 包含空字節的文件被打開默認为十六進制
“enable_hexadecimal_encoding”: true,

// Determines what character(s) are used to terminate each line in new files.
// Valid values are ‘system’ (whatever the OS uses), ‘windows’ (CRLF) and
// ‘unix’ (LF only).
// 每一行結束的時候用什麼字符做終止符
“default_line_ending”: “system”,

// When enabled, pressing tab will insert the best matching completion.
// When disabled, tab will only trigger snippets or insert a tab.
// Shift+tab can be used to insert an explicit tab when tab_completion is
// enabled.
// 設置为enabled時，在一個字符串間按Tab將插入一個制表符
// 設置为true時，按Tab會根據前後環境進行代碼自動匹配填補
“tab_completion”: true,

// Enable auto complete to be triggered automatically when typing.
// 代碼提示
“auto_complete”: true,

// The maximum file size where auto complete will be automatically triggered.
// 代碼提示的大小限制
“auto_complete_size_limit”: 4194304,

// The delay, in ms, before the auto complete window is shown after typing
// 代碼提示延遲顯示
“auto_complete_delay”: 50,

// Controls what scopes auto complete will be triggered in
// 代碼提示的控制範圍
“auto_complete_selector”: “source – comment”,

// Additional situations to trigger auto complete
// 觸發代碼提示的其他情況
“auto_complete_triggers”: [ {"selector": "text.html", "characters": "<"} ],

// By default, auto complete will commit the current completion on enter.
// This setting can be used to make it complete on tab instead.
// Completing on tab is generally a superior option, as it removes
// ambiguity between committing the completion and inserting a newline.
// 設为false時，選擇提示的代碼按回車或點擊可以輸出出來，但選擇true時不會輸出而是直接換行
“auto_complete_commit_on_tab”: false,

// Controls if auto complete is shown when snippet fields are active.
// Only relevant if auto_complete_commit_on_tab is true.
// auto_complete_commit_on_tab必須为true，控制代碼提示的活躍度（沒明白…）
“auto_complete_with_fields”: false,

// By default, shift+tab will only unindent if the selection spans
// multiple lines. When pressing shift+tab at other times, it’ll insert a
// tab character – this allows tabs to be inserted when tab_completion is
// enabled. Set this to true to make shift+tab always unindent, instead of
// inserting tabs.
// 設置为false，使用Shift + tab總是插入制表符
“shift_tab_unindent”: true,

// If true, the selected text will be copied into the find panel when it’s
// shown.
// On OS X, this value is overridden in the platform specific settings, so
// you’ll need to place this line in your user settings to override it.
// 選中的文本按Ctrl + f時，自動复制到查找面板的文本框裏
“find_selected_text”: true,

//
// User Interface Settings
//

// The theme controls the look of Sublime Text’s UI (buttons, tabs, scroll bars, etc)
// Data\Packages\Theme – Default\Default.sublime-theme控制軟件的主題
“theme”: “Default.sublime-theme”,

// Set to 0 to disable smooth scrolling. Set to a value between 0 and 1 to
// scroll slower, or set to larger than 1 to scroll faster
// 滾動的速度
“scroll_speed”: 1.0,

// Controls side bar animation when expanding or collapsing folders
// 左邊邊欄文件夾動畫
“tree_animation_enabled”: true,
// 標簽頁的關閉按鈕
“show_tab_close_buttons”: true,

// OS X 10.7 only: Set to true to disable Lion style full screen support.
// Sublime Text must be restarted for this to take effect.
// 針對OS X
“use_simple_full_screen”: false,

// Valid values are “system”, “enabled” and “disabled”
// 水平垂直滾動條：system和disabled为默認顯示方式，enabled为自動隱藏顯示
“overlay_scroll_bars”: “system”,

//
// Application Behavior Settings
//

// Exiting the application with hot_exit enabled will cause it to close
// immediately without prompting. Unsaved modifications and open files will
// be preserved and restored when next starting.
//
// Closing a window with an associated project will also close the window
// without prompting, preserving unsaved changes in the workspace file
// alongside the project.
// 熱推出功能！退出時不會提示是否保存文件，而是直接退出
// 下次打開軟件時，文件保持退出前的狀態，沒來得及保存的內容都在，但並沒有真實的寫在原文件裏
“hot_exit”: true,

// remember_open_files makes the application start up with the last set of
// open files. Changing this to false will have no effect if hot_exit is
// true
// 軟件使用最後的設定打開文件，hot_exit为true時沒有效果
“remember_open_files”: true,

// OS X only: When files are opened from finder, or by dragging onto the
// dock icon, this controls if a new window is created or not.
// 針對OS X
“open_files_in_new_window”: true,

// Set to true to close windows as soon as the last file is closed, unless
// there’s a folder open within the window. This is always enabled on OS X,
// changing it here won’t modify the behavior.
// 針對OS X
“close_windows_when_empty”: true,
// 哪些文件會被顯示到邊欄上
// folder_exclude_patterns and file_exclude_patterns control which files
// are listed in folders on the side bar. These can also be set on a per-
// project basis.
“folder_exclude_patterns”: [".svn", ".git", ".hg", "CVS"],
“file_exclude_patterns”: ["*.pyc", "*.pyo", "*.exe", "*.dll", "*.obj","*.o", "*.a", "*.lib", "*.so", "*.dylib", "*.ncb", "*.sdf", "*.suo", "*.pdb", "*.idb", ".DS_Store", "*.class", "*.psd", "*.db"],
// These files will still show up in the side bar, but won’t be included in
// Goto Anything or Find in Files
“binary_file_patterns”: ["*.jpg", "*.jpeg", "*.png", "*.gif", "*.ttf", "*.tga", "*.dds", "*.ico", "*.eot", "*.pdf", "*.swf", "*.jar", "*.zip"],

// List any packages to ignore here. When removing entries from this list,
// a restart may be required if the package contains plugins.
// 刪除你想要忽略的插件，需要重启
“ignored_packages”: ["Vintage"]
} 