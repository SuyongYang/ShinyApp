# Your first Shiny app {#basic-app}



## Introduction

In this chapter, we'll create a simple Shiny app.
I'll start by showing you the minimum boilerplate needed for a Shiny app, and then you'll learn how to start and stop it.
Next you'll learn the two key components of every Shiny app: the **UI** (short for user interface) which defines how your app *looks*, and the **server function** which defines how your app *works*.
Shiny uses **reactive programming** to automatically update outputs when inputs change so we'll finish off the chapter by learning the third important component of Shiny apps: reactive expressions.

If you haven't already installed Shiny, install it now with:


``` r
install.packages("shiny")
```

If you've already installed Shiny, use `packageVersion("shiny")` to check that you have version 1.5.0 or greater.

Then load in your current R session:


``` r
library(shiny)
```

## Create app directory and file {#create-app}

There are several ways to create a Shiny app.
The simplest is to create a new directory for your app, and put a single file called `app.R` in it.
This `app.R` file will be used to tell Shiny both how your app should look, and how it should behave.

Try it out by creating a new directory, and adding an `app.R` file that looks like this:


``` r
library(shiny)
ui <- fluidPage(
  "Hello, world!"
)
server <- function(input, output, session) {
}
shinyApp(ui, server)
```

This is a complete, if trivial, Shiny app!
Looking closely at the code above, our `app.R` does four things:

1.  It calls `library(shiny)` to load the shiny package.

2.  It defines the user interface, the HTML webpage that humans interact with.
    In this case, it's a page containing the words "Hello, world!".

3.  It specifies the behaviour of our app by defining a `server` function.
    It's currently empty, so our app doesn't *do* anything, but we'll be back to revisit this shortly.

4.  It executes `shinyApp(ui, server)` to construct and start a Shiny application from UI and server.

::: {.rmdnote}
**RStudio Tip**: There are two convenient ways to create a new app in RStudio:

-   Create a new directory and an `app.R` file containing a basic app in one step by clicking **File \| New Project**, then selecting **New Directory** and **Shiny Web Application**.

-   If you've already created the `app.R` file, you can quickly add the app boilerplate by typing "shinyapp" and pressing Shift+Tab.
:::

## Running and stopping {#running}

There are a few ways you can run this app:

-   Click the **Run App** (Figure \@ref(fig:run-app)) button in the document toolbar.

-   Use a keyboard shortcut: `Cmd/Ctrl` + `Shift` + `Enter`.

-   If you're not using RStudio, you can `(source())`[^basic-app-1] the whole document, or call `shiny::runApp()` with the path to the directory containing `app.R`.

[^basic-app-1]: The extra `()` on the outside are important.
    `shinyApp()` only creates an app when printed, and `()` forces the printing of the last result in the file being source, which is otherwise returned invisibly.

<div class="figure">
<img src="images/basic-app/run-app.png" alt="The Run App button can be found at the top-right of the source pane." width="617" />
<p class="caption">(\#fig:run-app)The Run App button can be found at the top-right of the source pane.</p>
</div>

Pick one of these options, and check that you see the same app as in Figure \@ref(fig:hello-world).
Congratulations!
You've made your first Shiny app.

<div class="figure">
<img src="images/basic-app/hello-world.png" alt="The very basic shiny app you'll see when you run the code above" width="440" />
<p class="caption">(\#fig:hello-world)The very basic shiny app you'll see when you run the code above</p>
</div>

Before you close the app, go back to RStudio and look at the R console.
You'll notice that it says something like:


``` r
#> Listening on http://127.0.0.1:3827
```

This tells you the URL where your app can be found: 127.0.0.1 is a standard address that means "this computer" and 3827 is a randomly assigned port number.
You can enter that URL into any compatible[^basic-app-2] web browser to open another copy of your app.

[^basic-app-2]: Shiny strives to support all modern browsers, and you can see the set currently supported at <https://www.rstudio.com/about/platform-support/>.
    Note that Internet Explorer versions prior to IE11 are not compatible when running Shiny directly from your R session.
    However, Shiny apps deployed on Shiny Server or ShinyApps.io can work with IE10 (earlier versions of IE are no longer supported).

Also notice that R is busy: the R prompt isn't visible, and the console toolbar displays a stop sign icon.
While a Shiny app is running, it "blocks" the R console.
This means that you can't run new commands at the R console until the Shiny app stops.

You can stop the app and return access to the console using any one of these options:

-   Click the stop sign icon on the R console toolbar.

-   Click on the console, then press `Esc` (or press `Ctrl` + `C` if you're not using RStudio).

-   Close the Shiny app window.

The basic workflow of Shiny app development is to write some code, start the app, play with the app, write some more code, and repeat.
If you're using RStudio, you don't even need to stop and re-start the app to see your changes --- you can either press the **Reload app** button in the toolbox or use the `Cmd/Ctrl` + `Shift` + `Enter` keyboard shortcut.
I'll cover other workflow patterns in Chapter \@ref(action-workflow).

## Adding UI controls {#adding-ui}

Next, we'll add some inputs and outputs to our UI so it's not *quite* so minimal.
We're going to make a very simple app that shows you all the built-in data frames included in the datasets package.

Replace your `ui` with this code:


``` r
ui <- fluidPage(
  selectInput("dataset", label = "Dataset", choices = ls("package:datasets")),
  verbatimTextOutput("summary"),
  tableOutput("table")
)
```

This example uses four new functions:

-   `fluidPage()` is a **layout function** that sets up the basic visual structure of the page.
    You'll learn more about them in Section \@ref(layout).

-   `selectInput()` is an **input control** that lets the user interact with the app by providing a value.
    In this case, it's a select box with the label "Dataset" and lets you choose one of the built-in datasets that come with R.
    You'll learn more about inputs in Section \@ref(inputs).

-   `verbatimTextOutput()` and `tableOutput()` are **output controls** that tell Shiny *where* to put rendered output (we'll get into the *how* in a moment).
    `verbatimTextOutput()` displays code and `tableOutput()` displays tables.
    You'll learn more about outputs in Section \@ref(outputs).

Layout functions, inputs, and outputs have different uses, but they are fundamentally the same under the covers: they're all just fancy ways to generate HTML, and if you call any of them outside of a Shiny app, you'll see HTML printed out at the console.
Don't be afraid to poke around to see how these various layouts and controls work under the hood.

Go ahead and run the app again.
You'll now see Figure \@ref(fig:basic-ui), a page containing a select box.
We only see the input, not the two outputs, because we haven't yet told Shiny how the input and outputs are related.
























