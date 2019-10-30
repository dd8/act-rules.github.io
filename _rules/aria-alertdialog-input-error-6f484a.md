---
id: 6f484a
name: `aria-alertdialog` identifies input error
rule_type: atomic

description: |
  This rule checks that `aria-alertdialog` is used to identify input errors.

accessibility_requirements: # Remove whatever is not applicable
  wcag-technique:ARIA18: # Using aria-alertdialog to Identify Errors
    forConformance: false
    failed: not satisfied
    passed: satisfied
    inapplicable: further testing needed

input_aspects:
  - DOM Tree
  - CSS Styling
  - Language

authors:
  - Carlos Duarte
  - João Vicente
---

## Applicability

The rule applies to each HTML or SVG element:

- that has one of the following [semantic roles](#semantic-role): `checkbox`, `combobox`, `listbox`, `menuitemcheckbox`, `menuitemradio`, `radio`, `searchbox`, `slider`, `spinbutton`, `switch` and `textbox`;
- for which [input errors](https://www.w3.org/TR/WCAG21/#dfn-input-error) are [automatically detected](#automatic-error-detection).

**Note**: The list of applicable [semantic roles](#semantic-role) is derived by taking all the [ARIA 1.1](https://www.w3.org/TR/wai-aria-1.1/) roles that:

- inherit from the [abstract](https://www.w3.org/TR/wai-aria/#abstract_roles) `input` or `select` role, and
- do not have a [required context](https://www.w3.org/TR/wai-aria/#scope) role that itself inherits from one of those roles.

## Expectation

After [user completion](#completed-input-field) of the target element or triggering the submission of the form if the target element belongs to one, each target element for which an [input error](https://www.w3.org/TR/WCAG21/#dfn-input-error) has been [automatically detected](#automatic-error-detection) causes an element with `role="alertdialog"` to become [visible](#visible), where the following is true:

- it has at least one [descendant](https://dom.spec.whatwg.org/#concept-tree-descendant) that is a [focusable](#focusable) element; and
- the focus moves to the [focusable](#focusable) element; and
- it is not possible to move the focus away from elements that are [descendents](https://dom.spec.whatwg.org/#concept-tree-descendant) of the element with `role="alertdialog"` until it no longer is [visible](#visible); and
- after the element with `role="alertdialog"` is no longer [visible](#visible) the focus returns to the position where it was before the element with `role="alertdialog"` became [visible](#visible); and
- the element with `role="alertdialog"` has an [accessible name](#accessible-name) that is not empty (`""`); and
- the text content of the element with `role="alertdialog"` identifies the [input error](https://www.w3.org/TR/WCAG21/#dfn-input-error).

**Note**: Information to identify an input error must include information about the element or elements in which the error occurred, and assist the user in understanding what was the cause of the error.

## Assumptions

_There are currently no assumptions_

## Accessibility Support

_There are no major accessibility support issues known for this rule._

## Background

- [ARIA18: Using aria-alertdialog to Identify Errors](https://www.w3.org/WAI/WCAG21/Techniques/aria/ARIA18)

## Test Cases

### Passed

#### Passed Example 1

alertdialog triggered on form submission presents message identifying the input error.

```html
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Using aria-alertdialog to Identify Errors</title>
		<script src="http://code.jquery.com/jquery.js"></script>
		<script>
			$(document).ready(function(e) {
				$('#trigger-alertdialog').click(function() {
					var errors = false
					var content = '<div id="firstElement" tabindex="0"></div><h1 id="alertHeading">Error</h1><div id="alertText">'
					if ($('#birth').val().length === 0) {
						content += 'Please fill age.<br>'
						errors = true
					}
					if ($('#hire').val().length === 0) {
						content += 'Please fill years on job.<br>'
						errors = true
					}
					if (!errors && Number($('#hire').val()) > Number($('#birth').val())) {
						content += 'Years on job is larger than age. Please verify age and years on job values.'
						errors = true
					}
					content +=
						'</div><button id="firstButton">Return to page and correct error</button><div id="lastElement" tabindex="0"></div>'
					if (errors) {
						$('main').attr('aria-hidden', 'true')
						var lastFocus = document.activeElement
						var modalOverlay = $('<div>').attr({ id: 'modalOverlay', tabindex: '0' })
						$(modalOverlay).appendTo('body')
						var dialog = $('<div>').attr({
							role: 'alertdialog',
							'aria-labelledby': 'alertHeading',
							'aria-describedby': 'alertText',
							tabindex: '0',
						})
						$(dialog)
							.html(content)
							.appendTo('body')
						$('#firstButton').focus()

						$('#lastElement').focusin(function(e) {
							$('#firstButton').focus()
						})
						$('#firstElement').focusin(function(e) {
							$('#firstButton').focus()
						})

						$('[role=alertdialog] button').click(function(e) {
							$('main').attr('aria-hidden', 'false')
							$(modalOverlay).remove()
							$(dialog).remove()
							lastFocus.focus()
						})
					}
					return false
				})
			})
		</script>
		<style type="text/css">
			#modalOverlay {
				width: 100%;
				height: 100%;
				z-index: 2;
				background-color: #000;
				opacity: 0.5;
				position: fixed;
				top: 0;
				left: 0;
				margin: 0;
				padding: 0;
			}

			[role='alertdialog'] {
				width: 50%;
				margin-left: auto;
				margin-right: auto;
				padding: 5px;
				border: thin #000 solid;
				background-color: #fff;
				z-index: 3;
				position: fixed;
				top: 25%;
				left: 25%;
			}
		</style>
	</head>

	<body>
		<main>
			<form>
				<label for="birth">Age (years)</label>
				<input type="number" id="birth" /><br />
				<label for="hire">Years on job</label>
				<input type="number" id="hire" /><br />
				<button id="trigger-alertdialog">Submit</button>
			</form>
		</main>
	</body>
</html>
```

#### Passed Example 2

alertdialog triggered on user completion presents message identifying the input error.

```html
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Using aria-alertdialog to Identify Errors</title>
		<script src="http://code.jquery.com/jquery.js"></script>
		<script>
			$(document).ready(function(e) {
				function showDialog(message) {
					var content = '<div id="firstElement" tabindex="0"></div><h1 id="alertHeading">Error</h1><div id="alertText">'
					content += message
					content +=
						'</div><button id="firstButton">Return to page and correct error</button><div id="lastElement" tabindex="0"></div>'
					$('main').attr('aria-hidden', 'true')
					var lastFocus = document.activeElement
					var modalOverlay = $('<div>').attr({ id: 'modalOverlay', tabindex: '0' })
					$(modalOverlay).appendTo('body')
					var dialog = $('<div>').attr({
						role: 'alertdialog',
						'aria-labelledby': 'alertHeading',
						'aria-describedby': 'alertText',
						tabindex: '0',
					})
					$(dialog)
						.html(content)
						.appendTo('body')
					$('#firstButton').focus()

					$('#lastElement').focusin(function(e) {
						$('#firstButton').focus()
					})
					$('#firstElement').focusin(function(e) {
						$('#firstButton').focus()
					})

					$('[role=alertdialog] button').click(function(e) {
						$('main').attr('aria-hidden', 'false')
						$(modalOverlay).remove()
						$(dialog).remove()
						lastFocus.focus()
					})
					return false
				}
				$('#birth').focusout(function() {
					if ($('#birth').val().length === 0) {
						showDialog('Please fill age.<br>')
					}
				})
				$('#hire').focusout(function() {
					if ($('#hire').val().length === 0) {
						showDialog('Please fill years on job.<br>')
					}
				})
			})
		</script>
		<style type="text/css">
			#modalOverlay {
				width: 100%;
				height: 100%;
				z-index: 2;
				background-color: #000;
				opacity: 0.5;
				position: fixed;
				top: 0;
				left: 0;
				margin: 0;
				padding: 0;
			}

			[role='alertdialog'] {
				width: 50%;
				margin-left: auto;
				margin-right: auto;
				padding: 5px;
				border: thin #000 solid;
				background-color: #fff;
				z-index: 3;
				position: fixed;
				top: 25%;
				left: 25%;
			}
		</style>
	</head>

	<body>
		<main>
			<form>
				<label for="birth">Age (years)</label>
				<input type="number" id="birth" /><br />
				<label for="hire">Years on job</label>
				<input type="number" id="hire" /><br />
				<button id="trigger-alertdialog">Submit</button>
			</form>
		</main>
	</body>
</html>
```

### Failed

#### Failed Example 1

alertdialog without focusable elements.

```html
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Using aria-alertdialog to Identify Errors</title>
		<script src="http://code.jquery.com/jquery.js"></script>
		<script>
			$(document).ready(function(e) {
				$('#trigger-alertdialog').click(function() {
					var errors = false
					var content = '</div><h1 id="alertHeading">Error</h1><div id="alertText">'
					if ($('#birth').val().length === 0) {
						content += 'Please fill age.<br>'
						errors = true
					}
					if ($('#hire').val().length === 0) {
						content += 'Please fill years on job.<br>'
						errors = true
					}
					if (!errors && Number($('#hire').val()) > Number($('#birth').val())) {
						content += 'Years on job is larger than age. Please verify age and years on job values.'
						errors = true
					}
					content += '</div><div id="firstButton">Click here or press Escape to return to page and correct error</div>'
					if (errors) {
						$('main').attr('aria-hidden', 'true')
						var lastFocus = document.activeElement
						var modalOverlay = $('<div>').attr({ id: 'modalOverlay', tabindex: '0' })
						$(modalOverlay).appendTo('body')
						var dialog = $('<div>').attr({
							role: 'alertdialog',
							'aria-labelledby': 'alertHeading',
							'aria-describedby': 'alertText',
							tabindex: '0',
						})
						$(dialog)
							.html(content)
							.appendTo('body')

						$('[role=alertdialog] #firstbutton').click(function(e) {
							$('main').attr('aria-hidden', 'false')
							$(modalOverlay).remove()
							$(dialog).remove()
							lastFocus.focus()
						})

						$('body').on('keyup', function(e) {
							if (e.key === 'Escape') {
								$('main').attr('aria-hidden', 'false')
								$(modalOverlay).remove()
								$(dialog).remove()
								lastFocus.focus()
							}
						})
					}
					return false
				})
			})
		</script>
		<style type="text/css">
			#modalOverlay {
				width: 100%;
				height: 100%;
				z-index: 2;
				background-color: #000;
				opacity: 0.5;
				position: fixed;
				top: 0;
				left: 0;
				margin: 0;
				padding: 0;
			}

			[role='alertdialog'] {
				width: 50%;
				margin-left: auto;
				margin-right: auto;
				padding: 5px;
				border: thin #000 solid;
				background-color: #fff;
				z-index: 3;
				position: fixed;
				top: 25%;
				left: 25%;
			}
		</style>
	</head>

	<body>
		<main>
			<form>
				<label for="birth">Age (years)</label>
				<input type="number" id="birth" /><br />
				<label for="hire">Years on job</label>
				<input type="number" id="hire" /><br />
				<button id="trigger-alertdialog">Submit</button>
			</form>
		</main>
	</body>
</html>
```

#### Failed Example 2

alertdialog opens without moving the focus to one of its focusable elements.

```html
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Using aria-alertdialog to Identify Errors</title>
		<script src="http://code.jquery.com/jquery.js"></script>
		<script>
			$(document).ready(function(e) {
				$('#trigger-alertdialog').click(function() {
					var errors = false
					var content = '<div id="firstElement" tabindex="0"></div><h1 id="alertHeading">Error</h1><div id="alertText">'
					if ($('#birth').val().length === 0) {
						content += 'Please fill age.<br>'
						errors = true
					}
					if ($('#hire').val().length === 0) {
						content += 'Please fill years on job.<br>'
						errors = true
					}
					if (!errors && Number($('#hire').val()) > Number($('#birth').val())) {
						content += 'Years on job is larger than age. Please verify age and years on job values.'
						errors = true
					}
					content +=
						'</div><button id="firstButton">Return to page and correct error</button><div id="lastElement" tabindex="0"></div>'
					if (errors) {
						$('main').attr('aria-hidden', 'true')
						var lastFocus = document.activeElement
						var modalOverlay = $('<div>').attr({ id: 'modalOverlay', tabindex: '0' })
						$(modalOverlay).appendTo('body')
						var dialog = $('<div>').attr({
							role: 'alertdialog',
							'aria-labelledby': 'alertHeading',
							'aria-describedby': 'alertText',
							tabindex: '0',
						})
						$(dialog)
							.html(content)
							.appendTo('body')

						$('#lastElement').focusin(function(e) {
							$('#firstButton').focus()
						})
						$('#firstElement').focusin(function(e) {
							$('#firstButton').focus()
						})

						$('[role=alertdialog] button').click(function(e) {
							$('main').attr('aria-hidden', 'false')
							$(modalOverlay).remove()
							$(dialog).remove()
							lastFocus.focus()
						})
					}
					return false
				})
			})
		</script>
		<style type="text/css">
			#modalOverlay {
				width: 100%;
				height: 100%;
				z-index: 2;
				background-color: #000;
				opacity: 0.5;
				position: fixed;
				top: 0;
				left: 0;
				margin: 0;
				padding: 0;
			}

			[role='alertdialog'] {
				width: 50%;
				margin-left: auto;
				margin-right: auto;
				padding: 5px;
				border: thin #000 solid;
				background-color: #fff;
				z-index: 3;
				position: fixed;
				top: 25%;
				left: 25%;
			}
		</style>
	</head>

	<body>
		<main>
			<form>
				<label for="birth">Age (years)</label>
				<input type="number" id="birth" /><br />
				<label for="hire">Years on job</label>
				<input type="number" id="hire" /><br />
				<button id="trigger-alertdialog">Submit</button>
			</form>
		</main>
	</body>
</html>
```

#### Failed Example 3

alertdialog that lets the focus move away from it.

```html
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Using aria-alertdialog to Identify Errors</title>
		<script src="http://code.jquery.com/jquery.js"></script>
		<script>
			$(document).ready(function(e) {
				$('#trigger-alertdialog').click(function() {
					var errors = false
					var content = '<div id="firstElement" tabindex="0"></div><h1 id="alertHeading">Error</h1><div id="alertText">'
					if ($('#birth').val().length === 0) {
						content += 'Please fill age.<br>'
						errors = true
					}
					if ($('#hire').val().length === 0) {
						content += 'Please fill years on job.<br>'
						errors = true
					}
					if (!errors && Number($('#hire').val()) > Number($('#birth').val())) {
						content += 'Years on job is larger than age. Please verify age and years on job values.'
						errors = true
					}
					content +=
						'</div><button id="firstButton">Return to page and correct error</button><div id="lastElement" tabindex="0"></div>'
					if (errors) {
						$('main').attr('aria-hidden', 'true')
						var lastFocus = document.activeElement
						var modalOverlay = $('<div>').attr({ id: 'modalOverlay', tabindex: '0' })
						$(modalOverlay).appendTo('body')
						var dialog = $('<div>').attr({
							role: 'alertdialog',
							'aria-labelledby': 'alertHeading',
							'aria-describedby': 'alertText',
							tabindex: '0',
						})
						$(dialog)
							.html(content)
							.appendTo('body')
						$('#firstButton').focus()

						$('[role=alertdialog] button').click(function(e) {
							$('main').attr('aria-hidden', 'false')
							$(modalOverlay).remove()
							$(dialog).remove()
							lastFocus.focus()
						})
					}
					return false
				})
			})
		</script>
		<style type="text/css">
			#modalOverlay {
				width: 100%;
				height: 100%;
				z-index: 2;
				background-color: #000;
				opacity: 0.5;
				position: fixed;
				top: 0;
				left: 0;
				margin: 0;
				padding: 0;
			}

			[role='alertdialog'] {
				width: 50%;
				margin-left: auto;
				margin-right: auto;
				padding: 5px;
				border: thin #000 solid;
				background-color: #fff;
				z-index: 3;
				position: fixed;
				top: 25%;
				left: 25%;
			}
		</style>
	</head>

	<body>
		<main>
			<form>
				<label for="birth">Age (years)</label>
				<input type="number" id="birth" /><br />
				<label for="hire">Years on job</label>
				<input type="number" id="hire" /><br />
				<button id="trigger-alertdialog">Submit</button>
			</form>
		</main>
	</body>
</html>
```

#### Failed Example 4

After closing the alertdialog the focus does not return to the element that had it before opening the alertdialog.

```html
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Using aria-alertdialog to Identify Errors</title>
		<script src="http://code.jquery.com/jquery.js"></script>
		<script>
			$(document).ready(function(e) {
				$('#trigger-alertdialog').click(function() {
					var errors = false
					var content = '<div id="firstElement" tabindex="0"></div><h1 id="alertHeading">Error</h1><div id="alertText">'
					if ($('#birth').val().length === 0) {
						content += 'Please fill age.<br>'
						errors = true
					}
					if ($('#hire').val().length === 0) {
						content += 'Please fill years on job.<br>'
						errors = true
					}
					if (!errors && Number($('#hire').val()) > Number($('#birth').val())) {
						content += 'Years on job is larger than age. Please verify age and years on job values.'
						errors = true
					}
					content +=
						'</div><button id="firstButton">Return to page and correct error</button><div id="lastElement" tabindex="0"></div>'
					if (errors) {
						$('main').attr('aria-hidden', 'true')
						var modalOverlay = $('<div>').attr({ id: 'modalOverlay', tabindex: '0' })
						$(modalOverlay).appendTo('body')
						var dialog = $('<div>').attr({
							role: 'alertdialog',
							'aria-labelledby': 'alertHeading',
							'aria-describedby': 'alertText',
							tabindex: '0',
						})
						$(dialog)
							.html(content)
							.appendTo('body')
						$('#firstButton').focus()

						$('#lastElement').focusin(function(e) {
							$('#firstButton').focus()
						})
						$('#firstElement').focusin(function(e) {
							$('#firstButton').focus()
						})

						$('[role=alertdialog] button').click(function(e) {
							$('main').attr('aria-hidden', 'false')
							$(modalOverlay).remove()
							$(dialog).remove()
							$('#birth').focus()
						})
					}
					return false
				})
			})
		</script>
		<style type="text/css">
			#modalOverlay {
				width: 100%;
				height: 100%;
				z-index: 2;
				background-color: #000;
				opacity: 0.5;
				position: fixed;
				top: 0;
				left: 0;
				margin: 0;
				padding: 0;
			}

			[role='alertdialog'] {
				width: 50%;
				margin-left: auto;
				margin-right: auto;
				padding: 5px;
				border: thin #000 solid;
				background-color: #fff;
				z-index: 3;
				position: fixed;
				top: 25%;
				left: 25%;
			}
		</style>
	</head>

	<body>
		<main>
			<form>
				<label for="birth">Age (years)</label>
				<input type="number" id="birth" /><br />
				<label for="hire">Years on job</label>
				<input type="number" id="hire" /><br />
				<button id="trigger-alertdialog">Submit</button>
			</form>
		</main>
	</body>
</html>
```

#### Failed Example 5

The element with `role="alertdialog"` does not have an accessible name.

```html
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Using aria-alertdialog to Identify Errors</title>
		<script src="http://code.jquery.com/jquery.js"></script>
		<script>
			$(document).ready(function(e) {
				$('#trigger-alertdialog').click(function() {
					var errors = false
					var content = '<div id="firstElement" tabindex="0"></div><h1 id="alertHeading">Error</h1><div id="alertText">'
					if ($('#birth').val().length === 0) {
						content += 'Please fill age.<br>'
						errors = true
					}
					if ($('#hire').val().length === 0) {
						content += 'Please fill years on job.<br>'
						errors = true
					}
					if (!errors && Number($('#hire').val()) > Number($('#birth').val())) {
						content += 'Years on job is larger than age. Please verify age and years on job values.'
						errors = true
					}
					content +=
						'</div><button id="firstButton">Return to page and correct error</button><div id="lastElement" tabindex="0"></div>'
					if (errors) {
						$('main').attr('aria-hidden', 'true')
						var lastFocus = document.activeElement
						var modalOverlay = $('<div>').attr({ id: 'modalOverlay', tabindex: '0' })
						$(modalOverlay).appendTo('body')
						var dialog = $('<div>').attr({ role: 'alertdialog', 'aria-describedby': 'alertText', tabindex: '0' })
						$(dialog)
							.html(content)
							.appendTo('body')
						$('#firstButton').focus()

						$('#lastElement').focusin(function(e) {
							$('#firstButton').focus()
						})
						$('#firstElement').focusin(function(e) {
							$('#firstButton').focus()
						})

						$('[role=alertdialog] button').click(function(e) {
							$('main').attr('aria-hidden', 'false')
							$(modalOverlay).remove()
							$(dialog).remove()
							lastFocus.focus()
						})
					}
					return false
				})
			})
		</script>
		<style type="text/css">
			#modalOverlay {
				width: 100%;
				height: 100%;
				z-index: 2;
				background-color: #000;
				opacity: 0.5;
				position: fixed;
				top: 0;
				left: 0;
				margin: 0;
				padding: 0;
			}

			[role='alertdialog'] {
				width: 50%;
				margin-left: auto;
				margin-right: auto;
				padding: 5px;
				border: thin #000 solid;
				background-color: #fff;
				z-index: 3;
				position: fixed;
				top: 25%;
				left: 25%;
			}
		</style>
	</head>

	<body>
		<main>
			<form>
				<label for="birth">Age (years)</label>
				<input type="number" id="birth" /><br />
				<label for="hire">Years on job</label>
				<input type="number" id="hire" /><br />
				<button id="trigger-alertdialog">Submit</button>
			</form>
		</main>
	</body>
</html>
```

#### Failed Example 6

The content of the alertdialog does not identify the error.

```html
<html>
	<head>
		<meta charset="UTF-8" />
		<title>Using aria-alertdialog to Identify Errors</title>
		<script src="http://code.jquery.com/jquery.js"></script>
		<script>
			$(document).ready(function(e) {
				$('#trigger-alertdialog').click(function() {
					var errors = false
					var content = '<div id="firstElement" tabindex="0"></div><h1 id="alertHeading">Error</h1><div id="alertText">'
					if ($('#birth').val().length === 0) {
						errors = true
					}
					if ($('#hire').val().length === 0) {
						errors = true
					}
					if (!errors && Number($('#hire').val()) > Number($('#birth').val())) {
						errors = true
					}
					if (errors) {
						content += 'Please fix the errors.<br>'
						content +=
							'</div><button id="firstButton">Return to page and correct error</button><div id="lastElement" tabindex="0"></div>'
						$('main').attr('aria-hidden', 'true')
						var lastFocus = document.activeElement
						var modalOverlay = $('<div>').attr({ id: 'modalOverlay', tabindex: '0' })
						$(modalOverlay).appendTo('body')
						var dialog = $('<div>').attr({
							role: 'alertdialog',
							'aria-labelledby': 'alertHeading',
							'aria-describedby': 'alertText',
							tabindex: '0',
						})
						$(dialog)
							.html(content)
							.appendTo('body')
						$('#firstButton').focus()

						$('#lastElement').focusin(function(e) {
							$('#firstButton').focus()
						})
						$('#firstElement').focusin(function(e) {
							$('#firstButton').focus()
						})

						$('[role=alertdialog] button').click(function(e) {
							$('main').attr('aria-hidden', 'false')
							$(modalOverlay).remove()
							$(dialog).remove()
							lastFocus.focus()
						})
					}
					return false
				})
			})
		</script>
		<style type="text/css">
			#modalOverlay {
				width: 100%;
				height: 100%;
				z-index: 2;
				background-color: #000;
				opacity: 0.5;
				position: fixed;
				top: 0;
				left: 0;
				margin: 0;
				padding: 0;
			}

			[role='alertdialog'] {
				width: 50%;
				margin-left: auto;
				margin-right: auto;
				padding: 5px;
				border: thin #000 solid;
				background-color: #fff;
				z-index: 3;
				position: fixed;
				top: 25%;
				left: 25%;
			}
		</style>
	</head>

	<body>
		<main>
			<form>
				<label for="birth">Age (years)</label>
				<input type="number" id="birth" /><br />
				<label for="hire">Years on job</label>
				<input type="number" id="hire" /><br />
				<button id="trigger-alertdialog">Submit</button>
			</form>
		</main>
	</body>
</html>
```

### Inapplicable

#### Inapplicable Example 1

No input element.

```html
<div></div>
```

#### Inapplicable Example 2

Form that does not include automatic detection of input errors.

```html
<form>
	<label for="text_field">Name (required)</label>
	<input type="text" id="text_field" />
	<input type="button" value="Submit" />
	<div id="error"></div>
</form>
```