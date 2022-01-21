# Motivation

-   Many algorithms can be decomposed into higher- and lower- level parts
-   Making tea can be decomposed into
    -   The process of making a hot beverage (boil water, pour into cup); and
    -   Tea-specific thinks (put a teabag into the water)
-   The high-level algorithm can then be reused for making coffee or hot chocolate
    -   Supported by beverage-specific strategies

# Strategy

Separates an algorithm into its 'skeleton' and concrete implementation steps, which can be varied at run-time.

# Problem

Normally you start with an app that solves 1 single task but with time you realize that there are a variety of users that can  benefit from your app if you change or add another step to your process. 
An example of this is an app that starts with the idea of serving users with a series of video tutorials that form a course. After a while you decide to add a different kind of course step, an article kind of step and later you add a quiz after each section. Your app starts to get unmanageable, many bugs arrive and by fixing one bug you create two.

# Solution

The Strategy pattern suggests that you take a class that does something specific in a lot of different ways and extract all of these algorithms into separate classes called _strategies_.

The main class is called the _context_ and its purpose is to store the reference of one of the strategies an delegates the task of executing the strategy to other object.

The client is responsible for telling the _context_ what strategy should be executed.

In our example of the courses app, we can have a Course class as the context and create many strategies like ArticleStrategy, VideoStrategy, QuizStrategy... We can make them implement a method called playStep which is in charge of playing the next step regardless of the type.

# Code Example

```go
package main

import (
	"fmt"
	"strings"
)

type OutputFormat int

const (
	Markdown OutputFormat = iota
	Html
)

type ListStrategy interface {
	Start(builder *strings.Builder)
	End(builder *strings.Builder)
	AddListItem(builder *strings.Builder, item string)
}

type MarkdownListStrategy struct{}

func (m *MarkdownListStrategy) Start(builder *strings.Builder) {

}

func (m *MarkdownListStrategy) End(builder *strings.Builder) {

}

func (m *MarkdownListStrategy) AddListItem(
	builder *strings.Builder, item string) {
	builder.WriteString(" * " + item + "\n")
}

type HtmlListStrategy struct{}

func (h *HtmlListStrategy) Start(builder *strings.Builder) {
	builder.WriteString("<ul>\n")
}

func (h *HtmlListStrategy) End(builder *strings.Builder) {
	builder.WriteString("</ul>\n")
}

func (h *HtmlListStrategy) AddListItem(builder *strings.Builder, item string) {
	builder.WriteString(" <li>" + item + "</li>\n")
}

// The context
type TextProcessor struct {
	builder strings.Builder
	listStrategy ListStrategy
}

func NewTextProcessor(listStrategy ListStrategy) *TextProcessor {
	return &TextProcessor{strings.Builder{}, listStrategy}
}

func (t *TextProcessor) SetOutputFormat(fmt OutputFormat) {
	switch fmt {
	case Markdown:
		t.listStrategy = &MarkdownListStrategy{}
	case Html:
		t.listStrategy = &HtmlListStrategy{}
	}
}

func (t *TextProcessor) AppendList(items []string) {
	t.listStrategy.Start(&t.builder)
	for _, item := range items {
		t.listStrategy.AddListItem(&t.builder, item)
	}
	t.listStrategy.End(&t.builder)
}

func (t *TextProcessor) Reset() {
	t.builder.Reset()
}

func (t *TextProcessor) String() string {
	return t.builder.String()
}

func main() {
	tp := NewTextProcessor(&MarkdownListStrategy{})
	tp.AppendList([]string{"foo", "bar", "baz"})
	fmt.Println(tp)
	tp.Reset()
	// Can swap the strategies during runtome
	tp.SetOutputFormat(Html)
	tp.AppendList([]string{"foo", "bar", "baz"})
	fmt.Println(tp)
}
``` 

# Applicability

* Use the Strategy pattern when you want to use different variants of an algorithm within an object and be able to switch from one algorithm to another during runtime.
* Use the Strategy when you have a lot of similar classes that only differ in the way they execute some behavior.
* Use the pattern to isolate the business logic of a class from the implementation details of algorithms that may not be as important in the context of that logic.
* Use the pattern when your class has a massive conditional operator that switches between different variants of the same algorithm.


# Pros and Cons

| Pros | Cons |
|--|--|
| You can swap algorithms used inside an object at runtime. | If you only have a couple of algorithms and they rarely change, there’s no real reason to overcomplicate the program with new classes and interfaces that come along with the pattern. |
| You can isolate the implementation details of an algorithm from the code that uses it. | Clients must be aware of the differences between strategies to be able to select a proper one. |
| You can replace inheritance with composition. | A lot of modern programming languages have functional type support that lets you implement different versions of an algorithm inside a set of anonymous functions. Then you could use these functions exactly as you’d have used the strategy objects, but without bloating your code with extra classes and interfaces. |
| _Open/Closed Principle_. You can introduce new strategies without having to change the context. |  |
