# Documentation Styleguide

A styleguide for writing documentation.

## The Structure
What makes for good documentation? While debatable, there is a formula which you can follow by answering the following questions.

### What is the subject?

Are you writing about binding to native events, working with Aurelia specific template control language or working with an API? These are examples of a subject.

### Describing the subject?

The subject is a broad overarching descriptor for the topic you are writing about. You then need to think about what this subject is comprised of. A subject can be broken up using a question based approach or it might be broken up into more loosely compiled explanations.

An example of a subject and what it is comprised of can be seen below:

Subject: Value Converters

- Explain what value converters are
- Explain how to create a basic value converter
- Explain how to create a value converter that can accept arguments
- Explain when you might want to use a value converter
- Showcase different scenarios where a value converter fits a common use-case

As a bare minimum, the questions you need to address for a subject are: what, how and why. The more detail, the better.

### Know your audience

When writing about a particular subject, you need to keep in mind who your target audience is. If you are writing about working with a specific aspect of Aurelia in a broad sense, a baseline assumption that the person reading has no prior knowledge of what you are talking about is a good starting point.

Never assume the reader has worked with the subject you are describing. You don't have to deliberately talk-down to the reader, but a simple explanation of any terms or concepts that might seem alien to the reader will go a long way to preventing frustration.

A safe baseline assumption is: the reader has no familiarity with Aurelia or Javascript frameworks, but has experiencing working with Javascript, CSS and HTML.

### Examples

Talking about a subject is one thing, but visually explaining the concept can help explain it even further. Where applicable, you should aim to have a working example that the reader can run as well as easy access to the source code for said example.

Screenshots should be avoided as they cannot be copied, they're not accessible and cannot be run.

Unless you are specifically showcasing an advanced Aurelia concept, examples should follow best practices and not rely on third-party dependencies (unless you're detailing an integration).
