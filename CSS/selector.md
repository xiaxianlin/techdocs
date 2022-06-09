# 选择器
### 主要分类

**CSS2**
- universal selector 
- type selectors
- descendant selectors
- child selectors
- the link pseudo-classes
- the user action pseudo-classes
- the target pseudo-class
- the :lang() pseudo-class
- adjacent selectors
- attribute selectors
- class selectors
- id selectors

**CSS3**
* structural pseudo-classess
* the ui element states pseudo-classes
* negation pseudo-class

### 模式匹配

**CSS2**
- *
- E
- E F
- E>F
- E:first-child
- E:link
- E:visited
- E:active
- E:hover
- E:focus
- E:lang(c)
- E + F
- E[foo]
- E[foo=“warning”]
- E[foo~=“warning”]
- E[lang|=“en”]
- DIV.warning
- E#id

**CSS3**
* E[foo^=“warning”]
* E[foo$=“warning”]
* E[foo*=“warning”]
* E:root
* E:nth-child(n)
* E:nth-last-child(n)
* E:nth-of-type(n)
* E:nth-last-of-type(n)
* E:last-child
* E:first-of-type
* E:last-of-type
* E:only-child
* E:only-of-type
* E:empty
* E:target
* E:enabled
* E:disabled
* E:checked
* E:not(s)
* E ~ F

### 伪元素

- :first-line
- :first-letter
- :before
- :after
