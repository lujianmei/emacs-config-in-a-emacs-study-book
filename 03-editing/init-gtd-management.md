<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. Agenda View</a>
<ul>
<li><a href="#sec-1-1">1.1. Agenda基本命令使用</a></li>
<li><a href="#sec-1-2">1.2. 对Agenda View进行修改</a></li>
<li><a href="#sec-1-3">1.3. Agenda View 导出</a></li>
<li><a href="#sec-1-4">1.4. Configuration</a></li>
<li><a href="#sec-1-5">1.5. Shot-key binding</a></li>
</ul>
</li>
</ul>
</div>
</div>


# Agenda View<a id="sec-1" name="sec-1"></a>

## Agenda基本命令使用<a id="sec-1-1" name="sec-1-1"></a>

1.  Agenda View用于以汇总视图的模式，组织单个或个多个文件中的任务，并可以按要求进行组织及排序，显示出当前各个项目的情况详情；Agenda view有如下几种视图
2.  按日历格式显示任务，可以指定日期、日期范围、按日、周、月、年来进行显示任务清单
    此模式目的是以当前日或者周来查看需要完成的工作清单；
3.  显示所有未完成的列表
    显示所有文件或项目中标记为TODO的任务列表，没有时间
4.  匹配按表头、标签、TODO状态等来显示任务清单
5.  按timeline模式显示，按时间排序
6.  搜索模式，按检索词的检索结果来组织显示数据
7.  问题项目或问题任务模式，显示各个项目中出问题或暂停的项目
8.  按自定义视图显示内容

9.  在Agenda View模式中，查看或修改文件：

当需要显示详细工作清单时，可以选择要查看的任务条目，点击 **TAB** 键，打开当前任务所在文件；如果需要修改，则在原文件中进行修改，修改完成后保存；切换到Agenda View中，按 **r** 键，则可以刷新修改后的内容；
在Agenda View中需要使用快捷键操作，具体可以参考： <http://orgmode.org/org.html#Agenda-commands>， 快捷键可以支持直接通过此视图修改原org文件中的状态；

1.  通过关键词搜索的方式显示
    可以采用搜索关键词的方式显示，搜索关键词可以为： ‘+computer +wifi -ethernet‘，即包含computer, wifi，不包含ethernet的任务清单；
2.  显示Stuck项目

## 对Agenda View进行修改<a id="sec-1-2" name="sec-1-2"></a>

1.  新增新的命令
    定义新的命令，来存储一些常用的搜索条件，定义需要显示的数据；此种方法可以按下面的代码形式，对Agenda Dispather进行定制：

    (setq org-agenda-custom-commands
               '(("x" agenda)
                 ("y" agenda*)
                 ("w" todo "WAITING")
                 ("W" todo-tree "WAITING")
                 ("u" tags "+boss-urgent")
                 ("v" tags-todo "+boss-urgent")
                 ("U" tags-tree "+boss-urgent")
                 ("f" occur-tree "\\<FIXME\\>")
                 ("h" . "HOME+Name tags searches") ; description for "h" prefix
                 ("hl" tags "+home+Lisa")
                 ("hp" tags "+home+Peter")
                 ("hk" tags "+home+Kim")))

如上面定义了一些新的命令，即可以通过 **C-c a x** 打开agenda，通过 **C-c a w** 打开只包含 "WAITING"的状态的任务清单；
1.  对现有命令的显示模式进行修改，采用block view的方式显示，即在一个buffer中，显示多个block，一次性查看不同的内容要求；

    (setq org-agenda-custom-commands
               '(("h" "Agenda and Home-related tasks"
                  ((agenda "")
                   (tags-todo "home")
                   (tags "garden")))
                 ("o" "Agenda and Office-related tasks"
                  ((agenda "")
                   (tags-todo "work")
                   (tags "office")))))

如上面定义，则在一个view buffer中，定义了不同的block，显示不同的内容， 如 **C-c a h** 则会显示三块内容，第一块显示agenda, 第二块显示包含"home"的todo标签的任务，第三个则是包含"garden"标签的任务；
1.  org-mode包含一些可定义的命令，可以用于支持对自定义的命令进行特殊的定制，这些定义默认是通过全局有效使用，如果需要对个别命令，采用不同的配置要求，则可以针对不同的命令进行设备；

    (setq org-agenda-custom-commands
               '(("w" todo "WAITING"
                  ((org-agenda-sorting-strategy '(priority-down))
                   (org-agenda-prefix-format "  Mixed: ")))
                 ("U" tags-tree "+boss-urgent"
                  ((org-show-context-detail 'minimal)))
                 ("N" search ""
                  ((org-agenda-files '("~org/notes.org"))
                   (org-agenda-text-search-extra-files nil)))))

如上面的定义，当执行 **C-c a w** 时显示只包含 "WAITING" 标签的任务，而再通过 **(org-agenda-sorting-strategy '(priority-down)** 来配置此view的排序条件为按优先级进行倒序排序；并增加 **Mixed:** 的前置；
另外，配置个性修改参数，可以为单命令级别进行配置，也可以为个别block进行单独配置命令，如：

    (setq org-agenda-custom-commands
              '(("h" "Agenda and Home-related tasks"
                 ((agenda)
                  (tags-todo "home")
                  (tags "garden"
                        ((org-agenda-sorting-strategy '(priority-up)))))
                 ((org-agenda-sorting-strategy '(priority-down))))
                ("o" "Agenda and Office-related tasks"
                 ((agenda)
                  (tags-todo "work")
                  (tags "office")))))

如上面命令，则是对 **C-c a h** 命令进行配置了整体 **((org-agenda-sorting-strategy '(priority-down)))**, 然而又单独对显示中的 **home** block进行配置 **((org-agenda-sorting-strategy '(priority-up)))** 的排序策略；
需要注意的是，参数中的值，可以是lisp语句，如果只是一个字符串，需要添加双引号；

1.  如果想要只针对某一种文本内容进行配置，则可以采用 **org-agenda-custom-commands-contexts** 进行配置，如：

    (setq org-agenda-custom-commands-contexts
               '(("o" (in-mode . "message-mode"))))

如上面命令，则只针对 **message-mode** 有效；
还可以将某一命令中，引用其它命令进行操作，如：

    (setq org-agenda-custom-commands-contexts
               '(("o" "r" (in-mode . "message-mode"))))

## Agenda View 导出<a id="sec-1-3" name="sec-1-3"></a>

Agenda view可以导出为text, html, pdf, postscript, icalendar格式；

## Configuration<a id="sec-1-4" name="sec-1-4"></a>

    ;;; init-my-org-custom-agenda-views.el --- Org customized views
    
    ;;; Commentary:
    
    ;;; Code:
    
    (message "start loading init-org-agenda-view")
    (setq-default org-agenda-clockreport-parameter-plist '(:link t :maxlevel 3))
    
    ;;; marked as a project will be a project
    
    (add-hook 'org-agenda-mode-hook 'hl-line-mode)
    
    
    ;;================================================================
    ;; Base config
    ;;================================================================
    
    (setq org-agenda-files (quote ("~/workspace/github/work-notes/personal"
                                   "~/workspace/github/work-notes/project-schedules"
                                   "~/workspace/github/work-notes/captures"
                                   )))
    
    ;; Set the agenda view to show the tasks on day/week/month/year
    (setq org-agenda-span 'week)
    ;; Set the agenda view to show the tasks on how many days, same effect as org-agenda-span
    (setq org-agenda-ndays 10)
    ;; If you want to set the start day of the agenda view, set following variable
    ;; Default is 0, means start from today, but if org-agenda-span set to week, deafult is start from Monday
    ;; (setq org-agenda-start-day "+10d")
    
    ;; Config what is a stuck project
                                            ;(setq org-stuck-projects
                                            ;      `(,active-project-match ("MAYBE")))
    
    
    ;; In order to include entries from the Emacs diary into Org mode's agenda
    ;;(setq org-agenda-include-diary t)
    
    
    ;; Custom commands for the agenda -- start with a clean slate.
    (setq org-agenda-custom-commands nil)
    
    ;; Do not dim blocked tasks
    (setq org-agenda-dim-blocked-tasks nil)
    
    ;; Compact the block agenda view
    ;;(setq org-agenda-compact-blocks t)
    
    (setq active-project-match "-INBOX/PROJECT")
    ;;================================================================
    ;; Special defun for First
    ;;================================================================
    
    (defconst leuven-org-completed-date-regexp
      (concat " \\("
              "CLOSED: \\[%Y-%m-%d"
              "\\|"
              "- State \"\\(DONE\\|CANX\\)\" * from .* \\[%Y-%m-%d"
              "\\|"
              "- State .* ->  *\"\\(DONE\\|CANX\\)\" * \\[%Y-%m-%d"
              "\\) ")
      "Matches any completion time stamp.")
    
    
    ;;================================================================
    ;; Special defun for First
    ;;================================================================
    ;; This lets me filter tasks with just / RET on the agenda which removes tasks I'm not supposed to be working on now from the list of returned results.This helps to keep my agenda clutter-free.
    (defun bh/org-auto-exclude-function (tag)
      "Automatic task exclusion in the agenda with / RET"
      (and (cond
            ((string= tag "hold")
             t)
            ((string= tag "farm")
             t))
           (concat "-" tag)))
    
    (setq org-agenda-auto-exclude-function 'bh/org-auto-exclude-function)
    
    
    ;; =========================================================================================
    ;; Config for All tasks
    ;; =========================================================================================
    (add-to-list 'org-agenda-custom-commands
                 `("c" . "任务汇总...") t)
    
    ;; Qingdao Projects Schedule
    (add-to-list 'org-agenda-custom-commands
                 `("cq" "Qingdao Projects"
                   ((alltodo ""))
                   ((org-agenda-files (list ,"~/workspace/github/work-notes/qingdao-projects")))
                   ((org-agenda-sorting-strategy '(priority-down time-down)))
                   ) t)
    
    ;; Personal schedules
    (add-to-list 'org-agenda-custom-commands
                 `("cp" "My Personal Schedule"
                   ((alltodo ""))
                   ((org-agenda-files (list ,"~/workspace/github/work-notes/personal")))
                   ((org-agenda-sorting-strategy '(priority-down time-down)))
                   ) t)
    ;; My projects
    (add-to-list 'org-agenda-custom-commands
                 `("cm" "My Project Schedules"
                   ((alltodo ""))
                   ((org-agenda-files (list ,"~/workspace/github/work-notes/project-schedules")))
                   ((org-agenda-sorting-strategy '(priority-down time-down)))
                   ) t)
    ;; =========================================================================================
    ;; Config for All tasks
    ;; =========================================================================================
    (add-to-list 'org-agenda-custom-commands
                 `("i" "Refile..."
                   ((org-agenda-files (list ,"~/workspace/github/work-notes/captures"))
                    ;; Refile
                    (tags "REFILE"
                          ((org-agenda-overriding-header "Tasks to Refile")
                           (org-tags-match-list-sublevels nil))))) t)
    
    ;; =========================================================================================
    ;; Config for Status list
    ;; =========================================================================================
    (add-to-list 'org-agenda-custom-commands
                 `("f" . "按进度状态查看...") t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("fq" "Qingdao Project Schedules"
                   (
                    ;; Events.
                    (agenda ""
                            ((org-agenda-entry-types '(:timestamp :sexp))
                             (org-agenda-overriding-header
                              (concat "CALENDAR Today "
                                      (format-time-string "%a %d" (current-time))
                                      ;; #("__________________" 0 12 (face (:foreground "gray")))
                                      ))
                             (org-agenda-span 'day)))
                    ;; Unscheduled new tasks (waiting to be prioritized and scheduled).
                    (tags-todo "LEVEL=2"
                               ((org-agenda-overriding-header "Qingdao Projects (Unscheduled)")
                                (org-agenda-files (list ,"~/workspace/github/work-notes/qingdao-projects"))
                                ))
                    ;; List of all TODO entries with deadline today.
                    (tags-todo "DEADLINE=\"<+0d>\""
                               ((org-agenda-overriding-header "DUE TODAY")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))
                                (org-agenda-sorting-strategy '(priority-down))))
                                            ; XXX Timed deadlines NOT shown!!!
                    ;; List of all TODO entries with deadline before today.
                    (tags-todo "DEADLINE<\"<+0d>\""
                               ((org-agenda-overriding-header "OVERDUE")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))
                                (org-agenda-sorting-strategy '(priority-down))))
                    ;; (agenda ""
                    ;;         ((org-agenda-entry-types '(:deadline))
                    ;;          (org-agenda-overriding-header "DUE DATES")
                    ;;          (org-agenda-skip-function
                    ;;           '(org-agenda-skip-entry-if 'todo 'done))
                    ;;          (org-agenda-sorting-strategy
                    ;;           '(priority-down time-down))
                    ;;          (org-agenda-span 'day)
                    ;;          (org-agenda-start-on-weekday nil)
                    ;;          (org-agenda-time-grid nil)))
                    (agenda ""
                            ((org-agenda-entry-types '(:scheduled))
                             (org-agenda-overriding-header "SCHEDULED")
                             (org-agenda-skip-function
                              '(org-agenda-skip-entry-if 'todo 'done))
                             (org-agenda-sorting-strategy
                              '(priority-down time-down))
                             (org-agenda-span 'day)
                             (org-agenda-files (list ,"~/workspace/github/work-notes/qingdao-projects"))
                             (org-agenda-start-on-weekday nil)
                             (org-agenda-time-grid nil)))
                    ;; List of all TODO entries completed today.
                    (todo "TODO|DONE|CANX" ; Includes repeated tasks (back in TODO).
                          ((org-agenda-overriding-header "COMPLETED TODAY")
                           (org-agenda-skip-function
                            '(org-agenda-skip-entry-if
                              'notregexp
                              (format-time-string leuven-org-completed-date-regexp)))
                           (org-agenda-sorting-strategy '(priority-down)))))
                   ((org-agenda-format-date "")
                    (org-agenda-files (list ,"~/workspace/github/work-notes/qingdao-projects"))
                    (org-agenda-start-with-clockreport-mode nil))) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("fm" "My Project Schedules"
                   (
                    ;; Events.
                    (agenda ""
                            ((org-agenda-entry-types '(:timestamp :sexp))
                             (org-agenda-overriding-header
                              (concat "CALENDAR Today "
                                      (format-time-string "%a %d" (current-time))
                                      ;; #("__________________" 0 12 (face (:foreground "gray")))
                                      ))
                             (org-agenda-span 'day)))
                    ;; Unscheduled new tasks (waiting to be prioritized and scheduled).
                    (tags-todo "LEVEL=2"
                               ((org-agenda-overriding-header "My Projects Schedule (Unscheduled)")
                                (org-agenda-files (list ,"~/workspace/github/work-notes/project-schedules"))
                                ))
                    ;; List of all TODO entries with deadline today.
                    (tags-todo "DEADLINE=\"<+0d>\""
                               ((org-agenda-overriding-header "DUE TODAY")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))
                                (org-agenda-sorting-strategy '(priority-down))))
                                            ; XXX Timed deadlines NOT shown!!!
                    ;; List of all TODO entries with deadline before today.
                    (tags-todo "DEADLINE<\"<+0d>\""
                               ((org-agenda-overriding-header "OVERDUE")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))
                                (org-agenda-sorting-strategy '(priority-down))))
                    ;; (agenda ""
                    ;;         ((org-agenda-entry-types '(:deadline))
                    ;;          (org-agenda-overriding-header "DUE DATES")
                    ;;          (org-agenda-skip-function
                    ;;           '(org-agenda-skip-entry-if 'todo 'done))
                    ;;          (org-agenda-sorting-strategy
                    ;;           '(priority-down time-down))
                    ;;          (org-agenda-span 'day)
                    ;;          (org-agenda-start-on-weekday nil)
                    ;;          (org-agenda-time-grid nil)))
                    (agenda ""
                            ((org-agenda-entry-types '(:scheduled))
                             (org-agenda-overriding-header "SCHEDULED")
                             (org-agenda-skip-function
                              '(org-agenda-skip-entry-if 'todo 'done))
                             (org-agenda-sorting-strategy
                              '(priority-down time-down))
                             (org-agenda-span 'day)
                             (org-agenda-files (list ,"~/workspace/github/work-notes/project-schedules"))
                             (org-agenda-start-on-weekday nil)
                             (org-agenda-time-grid nil)))
                    ;; List of all TODO entries completed today.
                    (todo "TODO|DONE|CANX" ; Includes repeated tasks (back in TODO).
                          ((org-agenda-overriding-header "COMPLETED TODAY")
                           (org-agenda-skip-function
                            '(org-agenda-skip-entry-if
                              'notregexp
                              (format-time-string leuven-org-completed-date-regexp)))
                           (org-agenda-sorting-strategy '(priority-down)))))
                   ((org-agenda-format-date "")
                    (org-agenda-files (list ,"~/workspace/github/work-notes/project-schedules"))
                    (org-agenda-start-with-clockreport-mode nil))) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("fp" "Personal Schedules"
                   (
                    ;; Events.
                    (agenda ""
                            ((org-agenda-entry-types '(:timestamp :sexp))
                             (org-agenda-overriding-header
                              (concat "CALENDAR Today "
                                      (format-time-string "%a %d" (current-time))
                                      ;; #("__________________" 0 12 (face (:foreground "gray")))
                                      ))
                             (org-agenda-span 'day)))
                    ;; Unscheduled new tasks (waiting to be prioritized and scheduled).
                    (tags-todo "LEVEL=2"
                               ((org-agenda-overriding-header "Personal Schedule(Unscheduled)")
                                (org-agenda-files (list ,"~/workspace/github/work-notes/personal"))
                                ))
                    ;; List of all TODO entries with deadline today.
                    (tags-todo "DEADLINE=\"<+0d>\""
                               ((org-agenda-overriding-header "DUE TODAY")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))
                                (org-agenda-sorting-strategy '(priority-down))))
                                            ; XXX Timed deadlines NOT shown!!!
                    ;; List of all TODO entries with deadline before today.
                    (tags-todo "DEADLINE<\"<+0d>\""
                               ((org-agenda-overriding-header "OVERDUE")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))
                                (org-agenda-sorting-strategy '(priority-down))))
                    ;; (agenda ""
                    ;;         ((org-agenda-entry-types '(:deadline))
                    ;;          (org-agenda-overriding-header "DUE DATES")
                    ;;          (org-agenda-skip-function
                    ;;           '(org-agenda-skip-entry-if 'todo 'done))
                    ;;          (org-agenda-sorting-strategy
                    ;;           '(priority-down time-down))
                    ;;          (org-agenda-span 'day)
                    ;;          (org-agenda-start-on-weekday nil)
                    ;;          (org-agenda-time-grid nil)))
                    (agenda ""
                            ((org-agenda-entry-types '(:scheduled))
                             (org-agenda-overriding-header "SCHEDULED")
                             (org-agenda-skip-function
                              '(org-agenda-skip-entry-if 'todo 'done))
                             (org-agenda-sorting-strategy
                              '(priority-down time-down))
                             (org-agenda-span 'day)
                             (org-agenda-files (list ,"~/workspace/github/work-notes/personal"))
                             (org-agenda-start-on-weekday nil)
                             (org-agenda-time-grid nil)))
                    ;; List of all TODO entries completed today.
                    (todo "TODO|DONE|CANX" ; Includes repeated tasks (back in TODO).
                          ((org-agenda-overriding-header "COMPLETED TODAY")
                           (org-agenda-skip-function
                            '(org-agenda-skip-entry-if
                              'notregexp
                              (format-time-string leuven-org-completed-date-regexp)))
                           (org-agenda-sorting-strategy '(priority-down)))))
                   ((org-agenda-format-date "")
                    (org-agenda-files (list ,"~/workspace/github/work-notes/personal"))
                    (org-agenda-start-with-clockreport-mode nil))) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("fh" "Hotlist"
                   ;; tags-todo "DEADLINE<=\"<+1w>\"|PRIORITY={A}|FLAGGED"
                   ((tags-todo "DEADLINE<\"<+0d>\""
                               ((org-agenda-overriding-header "OVERDUE")))
                    (tags-todo "DEADLINE>=\"<+0d>\"+DEADLINE<=\"<+1w>\""
                               ((org-agenda-overriding-header "DUE IN NEXT 7 DAYS")))
                    (tags-todo "DEADLINE=\"\"+PRIORITY={A}|DEADLINE>\"<+1w>\"+PRIORITY={A}"
                               ((org-agenda-overriding-header "HIGH PRIORITY")))
                    (tags-todo "DEADLINE=\"\"+FLAGGED|DEADLINE>\"<+1w>\"+FLAGGED"
                               ((org-agenda-overriding-header "FLAGGED")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-when-regexp-matches))
                                (org-agenda-skip-regexp "\\[#A\\]")))
                    ;; (tags-todo "DEADLINE=\"\"+PRIORITY<>{A}+FLAGGED|DEADLINE>\"<+1w>\"+PRIORITY<>{A}+FLAGGED"
                    ;;            ((org-agenda-overriding-header "...FLAGGED...")))
                    )
                   ((org-agenda-todo-ignore-scheduled 'future)
                    (org-agenda-sorting-strategy '(deadline-up)))) t) ; FIXME sort not OK
    
    ;; ========================================================================
    ;; Config for Checking by Date
    ;; ========================================================================
    (add-to-list 'org-agenda-custom-commands
                 `("r" . "按日期及状态查看...") t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("rq" "Qingdao Project All Tasks (grouped by Due Date)"
                   (
                    (tags-todo "DEADLINE<\"<+0d>\""
                               ((org-agenda-overriding-header "OVERDUE")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE=\"<+0d>\""
                               ((org-agenda-overriding-header "DUE TODAY")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE=\"<+1d>\""
                               ((org-agenda-overriding-header "DUE TOMORROW")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE>\"<+1d>\"+DEADLINE<=\"<+7d>\""
                               ((org-agenda-overriding-header "DUE WITHIN A WEEK")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE>\"<+7d>\"+DEADLINE<=\"<+28d>\""
                               ((org-agenda-overriding-header "DUE WITHIN A MONTH")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE>\"<+28d>\""
                               ((org-agenda-overriding-header "DUE LATER")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
    
                    ;; (todo ""
                    ;;            ((org-agenda-overriding-header "NO DUE DATE")
                    ;;             (org-agenda-skip-function
                    ;;              '(org-agenda-skip-entry-if 'deadline))))
                    (tags-todo "TODO={STRT}"
                               ((org-agenda-overriding-header "NO DUE DATE / STARTED")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'deadline))))
                    (tags-todo "TODO<>{STRT\\|WAIT\\|SDAY}"
                               ((org-agenda-overriding-header "NO DUE DATE / NEXT")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'deadline))))
                    (tags-todo "TODO={WAIT}"
                               ((org-agenda-overriding-header "NO DUE DATE / WAITING FOR")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'deadline))))
                    (tags-todo "TODO={SDAY}"
                               ((org-agenda-overriding-header "NO DUE DATE / SOMEDAY")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'deadline)))))
                   ((org-agenda-sorting-strategy '(priority-down time-down))
                    (org-agenda-files '("~/workspace/github/work-notes/qingdao-projects"))
                    (org-agenda-write-buffer-name "All Tasks (grouped by Due Date)"))
                   "~/org___all-tasks-by-due-date.pdf") t)
    
    
    (add-to-list 'org-agenda-custom-commands
                 `("rm" "My Project Schedules All Tasks (grouped by Due Date)"
                   (
    
                    (tags-todo "DEADLINE<\"<+0d>\""
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "OVERDUE")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE=\"<+0d>\""
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "DUE TODAY")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE=\"<+1d>\""
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "DUE TOMORROW")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE>\"<+1d>\"+DEADLINE<=\"<+7d>\""
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "DUE WITHIN A WEEK")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE>\"<+7d>\"+DEADLINE<=\"<+28d>\""
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "DUE WITHIN A MONTH")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE>\"<+28d>\""
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "DUE LATER")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
    
                    ;; (todo ""
                    ;;            ((org-agenda-overriding-header "NO DUE DATE")
                    ;;             (org-agenda-skip-function
                    ;;              '(org-agenda-skip-entry-if 'deadline))))
                    (tags-todo "TODO={STRT}"
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "NO DUE DATE / STARTED")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'deadline))))
                    (tags-todo "TODO<>{STRT\\|WAIT\\|SDAY}"
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "NO DUE DATE / NEXT")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'deadline))))
                    (tags-todo "TODO={WAIT}"
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "NO DUE DATE / WAITING FOR")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'deadline))))
                    (tags-todo "TODO={SDAY}"
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "NO DUE DATE / SOMEDAY")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'deadline)))))
                   ((org-agenda-sorting-strategy '(priority-down time-down))
                    (org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                    (org-agenda-write-buffer-name "All Tasks (grouped by Due Date)"))
                   "~/org___all-tasks-by-due-date.pdf") t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("rp" "My Personal Schedules All Tasks (grouped by Due Date)"
                   (
                    (tags-todo "DEADLINE<\"<+0d>\""
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "OVERDUE")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE=\"<+0d>\""
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "DUE TODAY")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE=\"<+1d>\""
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "DUE TOMORROW")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE>\"<+1d>\"+DEADLINE<=\"<+7d>\""
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "DUE WITHIN A WEEK")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE>\"<+7d>\"+DEADLINE<=\"<+28d>\""
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "DUE WITHIN A MONTH")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
                    (tags-todo "DEADLINE>\"<+28d>\""
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "DUE LATER")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'notdeadline))))
    
                    ;; (todo ""
                    ;;            ((org-agenda-overriding-header "NO DUE DATE")
                    ;;             (org-agenda-skip-function
                    ;;              '(org-agenda-skip-entry-if 'deadline))))
                    (tags-todo "TODO={STRT}"
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "NO DUE DATE / STARTED")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'deadline))))
                    (tags-todo "TODO<>{STRT\\|WAIT\\|SDAY}"
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "NO DUE DATE / NEXT")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'deadline))))
                    (tags-todo "TODO={WAIT}"
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "NO DUE DATE / WAITING FOR")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'deadline))))
                    (tags-todo "TODO={SDAY}"
                               (;;(org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                                (org-agenda-overriding-header "NO DUE DATE / SOMEDAY")
                                (org-agenda-skip-function
                                 '(org-agenda-skip-entry-if 'deadline)))))
                   ((org-agenda-sorting-strategy '(priority-down time-down))
                    (org-agenda-files '("~/workspace/github/work-notes/personal"))
                    (org-agenda-write-buffer-name "All Tasks (grouped by Due Date)"))
                   "~/org___all-tasks-by-due-date.pdf") t)
    
    
    ;; ===================================================================================
    ;; Config for Checking by Due Date
    ;; ===================================================================================
    (add-to-list 'org-agenda-custom-commands
                 `("d" . "按日期查看...") t)
    
    ;; Qingdao Projects
    (add-to-list 'org-agenda-custom-commands
                 `("dq" "Qingdao Projects All active tasks, by due date"
                   ((agenda ""
                            ((org-agenda-overriding-header "Today")
                             ;; FIXME We don't see "timed" DEADLINE.
                             (org-agenda-skip-function
                              (lambda ()
                                (let* ((dl (org-entry-get nil "DEADLINE")))
                                  (if (or (not dl)
                                          (equal dl "")
                                          (org-time> dl (org-time-today)))
                                      (progn (outline-next-heading) (point))))))
                             (org-agenda-skip-scheduled-if-deadline-is-shown t)
                             (org-agenda-span 'day)
                             (org-deadline-warning-days 0)))
                    (agenda ""
                            ((org-agenda-entry-types '(:deadline))
                             (org-agenda-overriding-header "Tomorrow")
                             (org-agenda-skip-function
                              '(leuven--skip-entry-unless-deadline-in-n-days-or-more 1))
                             (org-deadline-warning-days 1)))
                    (agenda ""
                            ((org-agenda-overriding-header "Next 5 days")
                             (org-agenda-skip-function
                              '(leuven--skip-entry-unless-deadline-in-n-days-or-more 2))
                             (org-deadline-warning-days 7)))
                    (agenda ""
                            ((org-agenda-format-date "")
                             (org-agenda-overriding-header "Next 3 weeks")
                             (org-agenda-skip-function
                              '(leuven--skip-entry-unless-deadline-in-n-days-or-more 7))
                             (org-deadline-warning-days 28))))
                   ((org-agenda-deadline-faces '((0.0 . default)))
                    (org-agenda-start-with-clockreport-mode nil)
                    (org-agenda-format-date "")
                    (org-agenda-span 'day)
                    (org-agenda-files '("~/workspace/github/work-notes/qingdao-projects"))
                    (org-agenda-sorting-strategy '(deadline-up))
                    (org-agenda-use-time-grid nil)
                    (org-agenda-write-buffer-name "Reminders"))) t)
    
    ;; Qingdao Projects
    (add-to-list 'org-agenda-custom-commands
                 `("dm" "My Projects All active tasks, by due date"
                   ((agenda ""
                            ((org-agenda-overriding-header "Today")
                             ;; FIXME We don't see "timed" DEADLINE.
                             (org-agenda-skip-function
                              (lambda ()
                                (let* ((dl (org-entry-get nil "DEADLINE")))
                                  (if (or (not dl)
                                          (equal dl "")
                                          (org-time> dl (org-time-today)))
                                      (progn (outline-next-heading) (point))))))
                             (org-agenda-skip-scheduled-if-deadline-is-shown t)
                             (org-agenda-span 'day)
                             (org-deadline-warning-days 0)))
                    (agenda ""
                            ((org-agenda-entry-types '(:deadline))
                             (org-agenda-overriding-header "Tomorrow")
                             (org-agenda-skip-function
                              '(leuven--skip-entry-unless-deadline-in-n-days-or-more 1))
                             (org-deadline-warning-days 1)))
                    (agenda ""
                            ((org-agenda-overriding-header "Next 5 days")
                             (org-agenda-skip-function
                              '(leuven--skip-entry-unless-deadline-in-n-days-or-more 2))
                             (org-deadline-warning-days 7)))
                    (agenda ""
                            ((org-agenda-format-date "")
                             (org-agenda-overriding-header "Next 3 weeks")
                             (org-agenda-skip-function
                              '(leuven--skip-entry-unless-deadline-in-n-days-or-more 7))
                             (org-deadline-warning-days 28))))
                   ((org-agenda-deadline-faces '((0.0 . default)))
                    (org-agenda-start-with-clockreport-mode nil)
                    (org-agenda-format-date "")
                    (org-agenda-span 'day)
                    (org-agenda-files '("~/workspace/github/work-notes/project-schedules"))
                    (org-agenda-sorting-strategy '(deadline-up))
                    (org-agenda-use-time-grid nil)
                    (org-agenda-write-buffer-name "Reminders"))) t)
    
    
    ;; Qingdao Projects
    (add-to-list 'org-agenda-custom-commands
                 `("dp" "Personal All active tasks, by due date"
                   ((agenda ""
                            ((org-agenda-overriding-header "Today")
                             ;; FIXME We don't see "timed" DEADLINE.
                             (org-agenda-skip-function
                              (lambda ()
                                (let* ((dl (org-entry-get nil "DEADLINE")))
                                  (if (or (not dl)
                                          (equal dl "")
                                          (org-time> dl (org-time-today)))
                                      (progn (outline-next-heading) (point))))))
                             (org-agenda-skip-scheduled-if-deadline-is-shown t)
                             (org-agenda-span 'day)
                             (org-deadline-warning-days 0)))
                    (agenda ""
                            ((org-agenda-entry-types '(:deadline))
                             (org-agenda-overriding-header "Tomorrow")
                             (org-agenda-skip-function
                              '(leuven--skip-entry-unless-deadline-in-n-days-or-more 1))
                             (org-deadline-warning-days 1)))
                    (agenda ""
                            ((org-agenda-overriding-header "Next 5 days")
                             (org-agenda-skip-function
                              '(leuven--skip-entry-unless-deadline-in-n-days-or-more 2))
                             (org-deadline-warning-days 7)))
                    (agenda ""
                            ((org-agenda-format-date "")
                             (org-agenda-overriding-header "Next 3 weeks")
                             (org-agenda-skip-function
                              '(leuven--skip-entry-unless-deadline-in-n-days-or-more 7))
                             (org-deadline-warning-days 28))))
                   ((org-agenda-deadline-faces '((0.0 . default)))
                    (org-agenda-start-with-clockreport-mode nil)
                    (org-agenda-format-date "")
                    (org-agenda-span 'day)
                    (org-agenda-files '("~/workspace/github/work-notes/personal"))
                    (org-agenda-sorting-strategy '(deadline-up))
                    (org-agenda-use-time-grid nil)
                    (org-agenda-write-buffer-name "Reminders"))) t)
    
    
    (defun leuven--skip-entry-unless-deadline-in-n-days-or-more (n)
      "Skip entries that have no deadline, or that have a deadline earlier than in N days."
      (let* ((dl (org-entry-get nil "DEADLINE")))
        (if (or (not dl)
                (equal dl "")
                (org-time< dl (+ (org-time-today) (* n 86400))))
            (progn (outline-next-heading) (point)))))
    
    (defun leuven--skip-entry-unless-overdue-deadline ()
      "Skip entries that have no deadline, or that have a deadline later than or equal to today."
      (let* ((dl (org-entry-get nil "DEADLINE")))
        (if (or (not dl)
                (equal dl "")
                (org-time>= dl (org-time-today)))
            (progn (outline-next-heading) (point)))))
    
    (defun leuven--skip-entry-if-past-deadline ()
      "Skip entries that have a deadline earlier than today."
      (let* ((dl (org-entry-get nil "DEADLINE")))
        (if (org-time< dl (org-time-today))
            (progn (outline-next-heading) (point)))))
    
    (defun leuven--skip-entry-if-deadline-in-less-than-n-days-or-schedule-in-less-than-n-days (n1 n2)
      "Skip entries that have a deadline in less than N1 days, or that have a
      scheduled date in less than N2 days, or that have no deadline nor scheduled."
      (let* ((dl (org-entry-get nil "DEADLINE"))
             (sd (org-entry-get nil "SCHEDULED")))
        (if (or (and dl
                     (not (equal dl ""))
                     (org-time< dl (+ (org-time-today) (* n1 86400))))
                (and sd
                     (not (equal sd ""))
                     (org-time< sd (+ (org-time-today) (* n2 86400))))
                (and (or (not dl)       ; No deadline.
                         (equal dl ""))
                     (or (not sd)       ; Nor scheduled.
                         (equal sd ""))))
            (progn (outline-next-heading) (point)))))
    
    (defun leuven--skip-entry-if-deadline-or-schedule ()
      "Skip entries that have a deadline or that have a scheduled date."
      (let* ((dl (org-entry-get nil "DEADLINE"))
             (sd (org-entry-get nil "SCHEDULED")))
        (if (or (and dl
                     (not (equal dl "")))
                (and sd
                     (not (equal sd ""))))
            (progn (outline-next-heading) (point)))))
    
    ;; ===================================================================================
    ;; Config for Checking by Priority
    ;; ===================================================================================
    
    (add-to-list 'org-agenda-custom-commands
                 `("p" . "按优先级查看完成状态...") t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("pq" "Qingdao Projects All Tasks (grouped by Priority)"
                   ((tags-todo "PRIORITY={A}"
                               ((org-agenda-overriding-header "HIGH")))
                    (tags-todo "PRIORITY={B}"
                               ((org-agenda-overriding-header "MEDIUM")))
                    (tags-todo "PRIORITY=\"\""
                               ((org-agenda-overriding-header "NONE"))) ; = Medium.
                    (tags-todo "PRIORITY={C}"
                               ((org-agenda-overriding-header "LOW")))
                    (todo "DONE|CANX"
                          ((org-agenda-overriding-header "COMPLETED")
                           (org-agenda-sorting-strategy '(priority-down))))
                    ((org-agenda-files '("~/workspace/github/work-notes/personal")))
                    )) t)
    ;; My projects
    (add-to-list 'org-agenda-custom-commands
                 `("pm" "My Projects All Tasks (grouped by Priority)"
                   ((tags-todo "PRIORITY={A}"
                               ((org-agenda-overriding-header "HIGH")))
                    (tags-todo "PRIORITY={B}"
                               ((org-agenda-overriding-header "MEDIUM")))
                    (tags-todo "PRIORITY=\"\""
                               ((org-agenda-overriding-header "NONE"))) ; = Medium.
                    (tags-todo "PRIORITY={C}"
                               ((org-agenda-overriding-header "LOW")))
                    (todo "DONE|CANX"
                          ((org-agenda-overriding-header "COMPLETED")
                           (org-agenda-sorting-strategy '(priority-down))))
                    ((org-agenda-files '("~/workspace/github/work-notes/project-schedules")))
                    )) t)
    
    ;; Personal
    (add-to-list 'org-agenda-custom-commands
                 `("pp" "My Projects All Tasks (grouped by Priority)"
                   ((tags-todo "PRIORITY={A}"
                               ((org-agenda-overriding-header "HIGH")))
                    (tags-todo "PRIORITY={B}"
                               ((org-agenda-overriding-header "MEDIUM")))
                    (tags-todo "PRIORITY=\"\""
                               ((org-agenda-overriding-header "NONE"))) ; = Medium.
                    (tags-todo "PRIORITY={C}"
                               ((org-agenda-overriding-header "LOW")))
                    (todo "DONE|CANX"
                          ((org-agenda-overriding-header "COMPLETED")
                           (org-agenda-sorting-strategy '(priority-down))))
                    ((org-agenda-files '("~/workspace/github/work-notes/personal")))
                    )) t)
    
    
    
    ;; ===================================================================================
    ;; Config for Checking by 时间
    ;; ===================================================================================
    
    (add-to-list 'org-agenda-custom-commands
                 `("j" . "Timesheet for Clocking...") t)
    
    ;; Show what happened today.
    (add-to-list 'org-agenda-custom-commands
                 `("jd" "Daily Timesheet"
                   ((agenda ""))
                   ((org-agenda-log-mode-items '(clock closed))
                    (org-agenda-overriding-header "DAILY TIMESHEET")
                    (org-agenda-show-log 'clockcheck)
                    (org-agenda-span 'day)
                    (org-agenda-start-with-clockreport-mode t)
                    (org-agenda-time-grid nil))) t)
    ;; Show what happened this week.
    (add-to-list 'org-agenda-custom-commands
                 `("jw" "Qingdao Weekly Timesheet"
                   ((agenda ""))
                   (
                    ;; (org-agenda-format-date "")
                    (org-agenda-overriding-header "WEEKLY TIMESHEET")
                    (org-agenda-skip-function '(org-agenda-skip-entry-if 'timestamp))
                    (org-agenda-span 'week)
                    (org-agenda-start-on-weekday 1)
                    (org-agenda-start-with-clockreport-mode t)
                    (org-agenda-time-grid nil))) t)
    
    ;; ===================================================================================
    ;; Config for Checking by Calendar
    ;; ===================================================================================
    
    (add-to-list 'org-agenda-custom-commands
                 `("k" . "Calendar...") t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("k7" "Events and appointments for 7 days"
                   ((agenda ""))
                   ((org-agenda-entry-types '(:timestamp :sexp))
                    ;; (org-agenda-overriding-header "Calendar for 7 days")
                    ;; (org-agenda-repeating-timestamp-show-all t)
                    (org-agenda-span 'week)
                    (org-agenda-format-date "\n%a %d")
                    ;; (org-agenda-date-weekend ... new face ...)
                    (org-agenda-files '("~/workspace/github/work-notes/personal"))
                    (org-agenda-time-grid nil))) t)
    
    ;; Calendar view for org-agenda.
    (when (locate-library "calfw-org")
    
      (autoload 'cfw:open-org-calendar "calfw-org"
        "Open an Org schedule calendar." t)
    
      (add-to-list 'org-agenda-custom-commands
                   `("km" "Calendar for current month"
                     (lambda (&rest ignore)
                       (cfw:open-org-calendar))) t)
    
      ;; (defun cfw:open-org-calendar-non-work (&args)
      ;;   (interactive)
      ;;   (let ((org-agenda-skip-function 'org-agenda-skip-work))
      ;;     (cfw:open-org-calendar)))
      ;;
      ;; (add-to-list 'org-agenda-custom-commands
      ;;              '("c" "Calendar (non-work) for current month"
      ;;                cfw:open-org-calendar-non-work) t)
    
      )
    (add-to-list 'org-agenda-custom-commands
                 `("u" . "Complete...") t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("uC" "Completed view"
                   (;; List of all TODO entries completed yesterday.
                    (todo "TODO|DONE|CANX" ; includes repeated tasks (back in TODO)
                          ((org-agenda-overriding-header
                            (concat "YESTERDAY   "
                                    (format-time-string "%a %d" (current-time-ndays-ago 1))
                                    ;; #("__________________" 0 12 (face (:foreground "gray")))
                                    ))
                           (org-agenda-skip-function
                            '(org-agenda-skip-entry-if
                              'notregexp
                              (format-time-string leuven-org-completed-date-regexp (current-time-ndays-ago 1))))
                           (org-agenda-sorting-strategy '(priority-down))))
                    ;; List of all TODO entries completed 2 days ago.
                    (todo "TODO|DONE|CANX" ; includes repeated tasks (back in TODO)
                          ((org-agenda-overriding-header
                            (concat "2 DAYS AGO  "
                                    (format-time-string "%a %d" (current-time-ndays-ago 2))))
                           (org-agenda-skip-function
                            '(org-agenda-skip-entry-if
                              'notregexp
                              (format-time-string leuven-org-completed-date-regexp (current-time-ndays-ago 2))))
                           (org-agenda-sorting-strategy '(priority-down))))
                    ;; List of all TODO entries completed 3 days ago.
                    (todo "TODO|DONE|CANX" ; Includes repeated tasks (back in TODO).
                          ((org-agenda-overriding-header
                            (concat "3 DAYS AGO  "
                                    (format-time-string "%a %d" (current-time-ndays-ago 3))))
                           (org-agenda-skip-function
                            '(org-agenda-skip-entry-if
                              'notregexp
                              (format-time-string leuven-org-completed-date-regexp (current-time-ndays-ago 3))))
                           (org-agenda-sorting-strategy '(priority-down)))))
                   ((org-agenda-format-date "")
                    (org-agenda-start-with-clockreport-mode nil))) t)
    
    (defun current-time-ndays-ago (n)
      "Return the current time minus N days."
      (time-subtract (current-time) (days-to-time n)))
    
    (add-to-list 'org-agenda-custom-commands
                 `("ux" "Completed tasks with no CLOCK lines"
                   ((todo "DONE|CANX"
                          ((org-agenda-overriding-header "Completed tasks with no CLOCK lines")
                           (org-agenda-skip-function
                            '(org-agenda-skip-entry-if
                              'regexp
                              (format-time-string "  CLOCK: .*--.* =>  .*")))
                           (org-agenda-sorting-strategy '(priority-down)))))) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("ur" "Recent items (past 7 days)"
                   ;; Faster than tags.
                   ((agenda ""))
                   ((org-agenda-start-day "-7d")
                    (org-agenda-span 7)
                    (org-agenda-repeating-timestamp-show-all nil)
                    ;; %s is only for agenda views
                    ;; (org-agenda-prefix-format "%s")
                    ;; maybe not make much difference ka
                    ;; (org-agenda-use-tag-inheritance nil)
                    (org-agenda-inactive-leader "Inactive:  ")
                    (org-agenda-include-inactive-timestamps t))) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("uw" "Weekly review"
                   ((tags "CATEGORY={@Collect}&LEVEL=2|TODO={NEW}"
                          ((org-agenda-overriding-header "NEW TASKS")))
    
                    (agenda ""
                            ((org-agenda-clockreport-mode t)
                             (org-agenda-format-date
                              (concat "\n"
                                      "%Y-%m-%d" " %a "
                                      (make-string (window-width) ?_)))
                             (org-agenda-overriding-header "PAST WEEK")
                             (org-agenda-prefix-format " %?-11t %i %-12:c% s")
                             (org-agenda-show-log 'clockcheck)
                             (org-agenda-span 7)
                             (org-agenda-start-day "-1w") ; recently done
                             (org-deadline-warning-days 0)))
    
                    (agenda ""
                            ((org-agenda-overriding-header "NEXT MONTH")
                             (org-agenda-span 'month)
                             (org-agenda-start-day "+0d")
                             (org-deadline-warning-days 0) ; XXX
                             ))
    
                    (todo "PROJ"
                          ((org-agenda-overriding-header "PROJECT LIST")))
    
                    ;; FIXME we should show which tasks (don't) have CLOCK lines: archived vs. deleted.
                    (todo "DONE|PROJDONE"
                          ((org-agenda-overriding-header
                            "Candidates to be archived")))
    
                    ;; (stuck ""
                    ;;        ((org-agenda-overriding-header "Stuck projects")))
    
                    (todo "STRT"
                          ((org-agenda-overriding-header "IN PROGRESS")
                           (org-agenda-todo-ignore-scheduled nil)))
    
                    (todo "TODO"        ; Don't include items from CollectBox! XXX
                          ((org-agenda-overriding-header "ACTION LIST")))
    
                    ;; Ignore scheduled and deadline entries, as they're visible
                    ;; in the above agenda (for the past + for next month) or
                    ;; scheduled/deadline'd for much later...
                    (todo "WAIT"
                          ((org-agenda-format-date "")
                           (org-agenda-overriding-header "WAITING FOR")
                           (org-agenda-todo-ignore-deadlines 'all) ; Future?
                           (org-agenda-todo-ignore-scheduled t)))
    
                    ;; Same reasoning as for WAIT.
                    (todo "SDAY"
                          ((org-agenda-format-date "")
                           (org-agenda-overriding-header "SOMEDAY")
                           (org-agenda-todo-ignore-deadlines 'all)
                           (org-agenda-todo-ignore-scheduled t)))
    
                    ;; ((org-agenda-start-with-clockreport-mode nil)
                    ;;  (org-agenda-prefix-format " %i %?-12t% s")
                    ;;  (org-agenda-write-buffer-name "Weekly task review"))
                    ;; "~/org-weekly-review.html") t)
                    )) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("uN" "Next"
                   ((tags-todo "TODO<>{SDAY}"))
                   ((org-agenda-overriding-header "List of all TODO entries with no due date (no SDAY)")
                    (org-agenda-skip-function '(org-agenda-skip-entry-if 'deadline))
                    (org-agenda-sorting-strategy '(priority-down)))) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("uW" "Waiting for"
                   ((tags-todo "TODO={WAIT}"))
                   ((org-agenda-overriding-header "Waiting for")
                    (org-agenda-sorting-strategy '(deadline-up)))) t) ; FIXME does not work.
    
    (add-to-list 'org-agenda-custom-commands
                 `("uP" "Projects"
                   ((tags-todo "project-DONE-CANX"))
                   ((org-agenda-overriding-header "Projects (High Level)")
                    (org-agenda-sorting-strategy nil))) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("+" . "MORE...") t)
    
    ;; Checking tasks that are assigned to me.
    (add-to-list 'org-agenda-custom-commands
                 `("+a" "Assigned to me"
                   ((tags ,(concat "Assignee={" user-login-name "\\|"
                                   user-mail-address "}")))
                   ((org-agenda-overriding-header "ASSIGNED TO ME"))) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("E" . "Exported agenda files...") t)
    
    ;; Exporting agenda views.
    (add-to-list 'org-agenda-custom-commands
                 `("Ea"
                   ((agenda ""))
                   (;; (org-tag-faces nil)
                    (ps-landscape-mode t)
                    (ps-number-of-columns 1))
                   ("~/workspace/github/publish-works/org-agenda.html" "~/workspace/github/publish-works/org-agenda.pdf")) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("Ep" "Call list"
                   ((tags-todo "phone"))
                   ((org-agenda-prefix-format " %-20:c [ ] " )
                    (org-agenda-remove-tags t)
                    ;; (org-agenda-with-colors nil)
                    (org-agenda-write-buffer-name
                     "Phone calls that you need to make")
                    (ps-landscape-mode t)
                    (ps-number-of-columns 1))
                   ("~/workspace/github/publish-works/org___calls.pdf")) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("A" . "ARCHIVE...") t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("Aa" "Archive"
                   ((tags-todo "ARCHIVE"))
                   ((org-agenda-todo-ignore-scheduled 'future)
                    (org-agenda-sorting-strategy '(deadline-down)))) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("R" . "REFERENCE...") t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("Rs" "Like s, but with extra files"
                   ((search ""))
                   ((org-agenda-text-search-extra-files
                     ;; FIXME Add `agenda-archives'
                     leuven-org-search-extra-files))) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("RS" "Like s, but only TODO entries"
                   ((search ""))
                   ((org-agenda-text-search-extra-files
                     ;; FIXME Add `agenda-archives'
                     leuven-org-search-extra-files))) t)
    
    (add-to-list 'org-agenda-custom-commands
                 `("Rn" "Organize thoughts to refile"
                   ((tags "refile|capture"))
                   ((org-agenda-overriding-header "Refile stuff"))) t)
    
    ;; Create a sparse tree (current buffer only) with all entries containing the
    ;; word `TODO', `FIXME' or `XXX'.
    (add-to-list 'org-agenda-custom-commands
                 `("1" "Task markers (in current buffer)"
                   ((occur-tree "\\<TODO\\|FIXME\\|XXX\\>"))) t)
    
    
    
    
    
    
    
    
    ;; -----------------------------------
    ;; base function for agenda view
    ;; -----------------------------------
    (defun bh/is-project-p ()
      "Any task with a todo keyword subtask"
      (save-restriction
        (widen)
        (let ((has-subtask)
              (subtree-end (save-excursion (org-end-of-subtree t)))
              (is-a-task (member (nth 2 (org-heading-components)) org-todo-keywords-1)))
          (save-excursion
            (forward-line 1)
            (while (and (not has-subtask)
                        (< (point) subtree-end)
                        (re-search-forward "^\*+ " subtree-end t))
              (when (member (org-get-todo-state) org-todo-keywords-1)
                (setq has-subtask t))))
          (and is-a-task has-subtask))))
    
    (defun bh/is-project-subtree-p ()
      "Any task with a todo keyword that is in a project subtree.
    Callers of this function already widen the buffer view."
      (let ((task (save-excursion (org-back-to-heading 'invisible-ok)
                                  (point))))
        (save-excursion
          (bh/find-project-task)
          (if (equal (point) task)
              nil
            t))))
    
    (defun bh/is-task-p ()
      "Any task with a todo keyword and no subtask"
      (save-restriction
        (widen)
        (let ((has-subtask)
              (subtree-end (save-excursion (org-end-of-subtree t)))
              (is-a-task (member (nth 2 (org-heading-components)) org-todo-keywords-1)))
          (save-excursion
            (forward-line 1)
            (while (and (not has-subtask)
                        (< (point) subtree-end)
                        (re-search-forward "^\*+ " subtree-end t))
              (when (member (org-get-todo-state) org-todo-keywords-1)
                (setq has-subtask t))))
          (and is-a-task (not has-subtask)))))
    
    (defun bh/is-subproject-p ()
      "Any task which is a subtask of another project"
      (let ((is-subproject)
            (is-a-task (member (nth 2 (org-heading-components)) org-todo-keywords-1)))
        (save-excursion
          (while (and (not is-subproject) (org-up-heading-safe))
            (when (member (nth 2 (org-heading-components)) org-todo-keywords-1)
              (setq is-subproject t))))
        (and is-a-task is-subproject)))
    
    (defun bh/list-sublevels-for-projects-indented ()
      "Set org-tags-match-list-sublevels so when restricted to a subtree we list all subtasks.
      This is normally used by skipping functions where this variable is already local to the agenda."
      (if (marker-buffer org-agenda-restrict-begin)
          (setq org-tags-match-list-sublevels 'indented)
        (setq org-tags-match-list-sublevels nil))
      nil)
    
    (defun bh/list-sublevels-for-projects ()
      "Set org-tags-match-list-sublevels so when restricted to a subtree we list all subtasks.
      This is normally used by skipping functions where this variable is already local to the agenda."
      (if (marker-buffer org-agenda-restrict-begin)
          (setq org-tags-match-list-sublevels t)
        (setq org-tags-match-list-sublevels nil))
      nil)
    
    (defvar bh/hide-scheduled-and-waiting-next-tasks t)
    
    (defun bh/toggle-next-task-display ()
      (interactive)
      (setq bh/hide-scheduled-and-waiting-next-tasks (not bh/hide-scheduled-and-waiting-next-tasks))
      (when  (equal major-mode 'org-agenda-mode)
        (org-agenda-redo))
      (message "%s WAITING and SCHEDULED NEXT Tasks" (if bh/hide-scheduled-and-waiting-next-tasks "Hide" "Show")))
    
    (defun bh/skip-stuck-projects ()
      "Skip trees that are not stuck projects"
      (save-restriction
        (widen)
        (let ((next-headline (save-excursion (or (outline-next-heading) (point-max)))))
          (if (bh/is-project-p)
              (let* ((subtree-end (save-excursion (org-end-of-subtree t)))
                     (has-next ))
                (save-excursion
                  (forward-line 1)
                  (while (and (not has-next) (< (point) subtree-end) (re-search-forward "^\\*+ NEXT " subtree-end t))
                    (unless (member "WAITING" (org-get-tags-at))
                      (setq has-next t))))
                (if has-next
                    nil
                  next-headline)) ; a stuck project, has subtasks but no next task
            nil))))
    
    (defun bh/skip-non-stuck-projects ()
      "Skip trees that are not stuck projects"
      ;; (bh/list-sublevels-for-projects-indented)
      (save-restriction
        (widen)
        (let ((next-headline (save-excursion (or (outline-next-heading) (point-max)))))
          (if (bh/is-project-p)
              (let* ((subtree-end (save-excursion (org-end-of-subtree t)))
                     (has-next ))
                (save-excursion
                  (forward-line 1)
                  (while (and (not has-next) (< (point) subtree-end) (re-search-forward "^\\*+ NEXT " subtree-end t))
                    (unless (member "WAITING" (org-get-tags-at))
                      (setq has-next t))))
                (if has-next
                    next-headline
                  nil)) ; a stuck project, has subtasks but no next task
            next-headline))))
    
    (defun bh/skip-non-projects ()
      "Skip trees that are not projects"
      ;; (bh/list-sublevels-for-projects-indented)
      (if (save-excursion (bh/skip-non-stuck-projects))
          (save-restriction
            (widen)
            (let ((subtree-end (save-excursion (org-end-of-subtree t))))
              (cond
               ((bh/is-project-p)
                nil)
               ((and (bh/is-project-subtree-p) (not (bh/is-task-p)))
                nil)
               (t
                subtree-end))))
        (save-excursion (org-end-of-subtree t))))
    
    (defun bh/skip-non-tasks ()
      "Show non-project tasks.
    Skip project and sub-project tasks, habits, and project related tasks."
      (save-restriction
        (widen)
        (let ((next-headline (save-excursion (or (outline-next-heading) (point-max)))))
          (cond
           ((bh/is-task-p)
            nil)
           (t
            next-headline)))))
    
    (defun bh/skip-project-trees-and-habits ()
      "Skip trees that are projects"
      (save-restriction
        (widen)
        (let ((subtree-end (save-excursion (org-end-of-subtree t))))
          (cond
           ((bh/is-project-p)
            subtree-end)
           ((org-is-habit-p)
            subtree-end)
           (t
            nil)))))
    
    (defun bh/skip-projects-and-habits-and-single-tasks ()
      "Skip trees that are projects, tasks that are habits, single non-project tasks"
      (save-restriction
        (widen)
        (let ((next-headline (save-excursion (or (outline-next-heading) (point-max)))))
          (cond
           ((org-is-habit-p)
            next-headline)
           ((and bh/hide-scheduled-and-waiting-next-tasks
                 (member "WAITING" (org-get-tags-at)))
            next-headline)
           ((bh/is-project-p)
            next-headline)
           ((and (bh/is-task-p) (not (bh/is-project-subtree-p)))
            next-headline)
           (t
            nil)))))
    
    (defun bh/skip-project-tasks-maybe ()
      "Show tasks related to the current restriction.
    When restricted to a project, skip project and sub project tasks, habits, NEXT tasks, and loose tasks.
    When not restricted, skip project and sub-project tasks, habits, and project related tasks."
      (save-restriction
        (widen)
        (let* ((subtree-end (save-excursion (org-end-of-subtree t)))
               (next-headline (save-excursion (or (outline-next-heading) (point-max))))
               (limit-to-project (marker-buffer org-agenda-restrict-begin)))
          (cond
           ((bh/is-project-p)
            next-headline)
           ((org-is-habit-p)
            subtree-end)
           ((and (not limit-to-project)
                 (bh/is-project-subtree-p))
            subtree-end)
           ((and limit-to-project
                 (bh/is-project-subtree-p)
                 (member (org-get-todo-state) (list "NEXT")))
            subtree-end)
           (t
            nil)))))
    
    (defun bh/skip-project-tasks ()
      "Show non-project tasks.
    Skip project and sub-project tasks, habits, and project related tasks."
      (save-restriction
        (widen)
        (let* ((subtree-end (save-excursion (org-end-of-subtree t))))
          (cond
           ((bh/is-project-p)
            subtree-end)
           ((org-is-habit-p)
            subtree-end)
           ((bh/is-project-subtree-p)
            subtree-end)
           (t
            nil)))))
    
    (defun bh/skip-non-project-tasks ()
      "Show project tasks.
    Skip project and sub-project tasks, habits, and loose non-project tasks."
      (save-restriction
        (widen)
        (let* ((subtree-end (save-excursion (org-end-of-subtree t)))
               (next-headline (save-excursion (or (outline-next-heading) (point-max)))))
          (cond
           ((bh/is-project-p)
            next-headline)
           ((org-is-habit-p)
            subtree-end)
           ((and (bh/is-project-subtree-p)
                 (member (org-get-todo-state) (list "NEXT")))
            subtree-end)
           ((not (bh/is-project-subtree-p))
            subtree-end)
           (t
            nil)))))
    
    (defun bh/skip-projects-and-habits ()
      "Skip trees that are projects and tasks that are habits"
      (save-restriction
        (widen)
        (let ((subtree-end (save-excursion (org-end-of-subtree t))))
          (cond
           ((bh/is-project-p)
            subtree-end)
           ((org-is-habit-p)
            subtree-end)
           (t
            nil)))))
    
    (defun bh/skip-non-subprojects ()
      "Skip trees that are not projects"
      (let ((next-headline (save-excursion (outline-next-heading))))
        (if (bh/is-subproject-p)
            nil
          next-headline)))
    
    
    (provide 'init-org-agenda-view)
    
    ;;; init-org-agenda-views.el ends here

## Shot-key binding<a id="sec-1-5" name="sec-1-5"></a>
