---
title: "TIL: Using Go's selenium package to wait for something"
date: 2022-01-08
tags:
  - til
  - golang
  - selenium
layout: layouts/post.njk
---

Recently at work a product I support had a JS issue that showed up in our production environment but did not show up in our staging environment. I wanted to develop some simple Selenium testing to help detect these issues in prod with less human interaction.

One issue I ran into developing these WebDriver tests using [the Go WebDriver client](https://pkg.go.dev/github.com/tebeka/selenium) was a full-screen preloader element that would intercept clicks intended for other UI elements. The client provides this option to deal with this issue:

```go
type WebDriver interface {
	...
	Wait(condition Condition) error
	...
}

...

type Condition func(wd WebDriver) (bool, error)
```

So to deal with my particular issue, you can write a function like:

```go
func isPreloaderHidden(wd selenium.WebDriver) (bool, error) {
	plElem, err := wd.GetElement(selenium.ByCSSSelector, "#preloader")
	if err != nil {
		return false, err
	}

	displayed, err := plElem.IsDisplayed()
	return !displayed, err
}
```

And then add the following code to your test:

```go
func TestSomething(t *testing.T) {
	//setup happens up here
	//...

	// wait for the preloader to be hidden
	if err := wd.Wait(isPreloaderHidden); err != nil {
		t.Error(err)
	}

	// once we get out here, we can safely try to click our element
	if err := someElemWeTest.Click(); err != nil {
		t.Fatal(err)
	}
}
```

I haven't actually tested this, but for a common wait condition, you could write a wrapper like:

```go
func clickAfterCondition(wd selenium.WebDriver, cond selenium.Condition, elem selenium.WebElement) error {
	if err := wd.Wait(cond); err != nil { 
		return err
	}

	return elem.Click()
}
```

Optionally you could write a wrapper with a static condition as well.
