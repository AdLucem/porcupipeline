# Porcupipeline

## Reasons For Using The Haskell Base

- It's the only mature, in-active-dev software that does at least part of what I want.

- I know the people working on it, and they're very responsive on chat. So if we discover a snag in the software/thing we don't understand, I can just ping them and get a quick answer. Also, I *am* people working on it.

- Type system and type safety. I can't provide a very concrete example of this, but let's just say that... you know those weird-ass runtime errors you'll discover in python when you're 3-4ths of the way through a 2-hour processing task? Yeah. Those don't happen in Haskell. Your program does precisely what you tell it to do, if it compiles.


## Contextual Word Vectorization Pipeline

Vectorize is a function.

```
Vectorize ::
	- Input :: DataSource
	- Output :: DataSink
```

DataSource is a type (provided by Porcupine). Think of it like a C struct.

```
DataSource ::
	- type_of_data :: Sentence
```

The type of data coming from our source is a sentence, so here's Sentence, which is an user-defined type:

```
Sentence :: 
	- Index :: Int
	- Tokens :: [String]
```

DataSink is also like DataSource, except it takes a parameter (which is also a type- like DataSource) that defines the structure into which we put the result of our computation.

That structure- user-defined - is a Vector.

```
DataSink ::
	- type_of_data :: Vector

Vector :: [Float]
```

(Helpful note: haskell has a pretty well-defined vector library. We don't *really* have to define the `Vector` type ourselves. These are example definitions.)

What we want to do can be described as:
	- Load data into the DataSource type. (If putting data 'into' types is weird, think of DataSource as a type of box into which we put our messy inputs, so that we can have lots of neat boxes to operate on instead of messy data)
	- Pass DataSource to Vectorize, which will do vector-y things with our data. Think of it like this: We now have a warehouse full of boxes, so we send all the boxes to the processing plant, which is `Vectorize`.
	- 'Return' the data from `Vectorize` into `DataSink`

This is a 'pipeline'. Ideally, we'd like to run several of these with slightly different internal details- say, I wanted to replace my current `Vectorize` function with another function, `BetterVectorize`. I don't want to copy-paste the pipeline each time. It would be nice if there was some sort of pipeline abstraction in which to place these.

Porcupine gives us that abstraction. It's a type called `PTask`. This might be a little confusing because in Haskell you can represent a function, or even a series of steps, using a single type- think of it as a thing in which we put our pipeline so that it's 'safe' and we don't have to care about the details.

So we define a function with the type `PTask`, that holds our nice little pipeline in it: (this is not syntactically accurate, but don't worry about that)

```
analyseOneUser :: PTask
analyseOneUser =
loadData userFile -> computeAnalysis -> writeData analysisFile
```

