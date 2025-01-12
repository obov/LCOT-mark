<style>
.custom {
    background-color: #008d8d;
    color: white;
    padding: 0.25em 0.5em 0.25em 0.5em;
    white-space: pre-wrap;       /* css-3 */
    white-space: -moz-pre-wrap;  /* Mozilla, since 1999 */
    white-space: -pre-wrap;      /* Opera 4-6 */
    white-space: -o-pre-wrap;    /* Opera 7 */
    word-wrap: break-word;
}

pre {
    background-color: #027c7c;
    padding-left: 0.5em;
}

</style>

# CSV Loader

- Author: [JoonHo Kim](https://github.com/jhboyo)
- Design: []()
- Peer Review : [syshin0116](https://github.com/syshin0116), [syshin0116](https://github.com/syshin0116), [forwardyoung](https://github.com/forwardyoung)
- This is a part of [LangChain Open Tutorial](https://github.com/LangChain-OpenTutorial/LangChain-OpenTutorial)

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/LangChain-OpenTutorial/LangChain-OpenTutorial/blob/main/06-DocumentLoader/04-CSV-Loader.ipynb) [![Open in GitHub](https://img.shields.io/badge/Open%20in%20GitHub-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/LangChain-OpenTutorial/LangChain-OpenTutorial/blob/main/06-DocumentLoader/04-CSV-Loader.ipynb)


## Overview

This tutorial provides a comprehensive guide on how to use the `CSVLoader` utility in LangChain to seamlessly integrate data from CSV files into your applications. The `CSVLoader` is a powerful tool for processing structured data, enabling developers to extract, parse, and utilize information from CSV files within the LangChain framework.

[Comma-Separated Values (CSV)](https://en.wikipedia.org/wiki/Comma-separated_values) is one of the most common formats for storing and exchanging data.

`CSVLoader` simplifies the process of loading, parsing, and extracting data from CSV files, allowing developers to seamlessly incorporate this information into LangChain workflows.


### Table of Contents

- [Overview](#overview)
- [Environment Setup](#environment-setup)
- [How to load CSVs](#how-to-load-csvs)
- [Customizing the CSV parsing and loading](#customizing-the-csv-parsing-and-loading)
- [Specify a column to identify the document source](#specify-a-column-to-identify-the-document-source)
- [Generating XML document format](#generating-xml-document-format)
- [UnstructuredCSVLoader](#unstructuredcsvloader)
- [DataFrameLoader](#dataframeloader)


### References

- [Langchain CSVLoader](https://python.langchain.com/api_reference/community/document_loaders/langchain_community.document_loaders.csv_loader.CSVLoader.html)
- [Langchain How to load CSVs](https://python.langchain.com/docs/how_to/document_loader_csv)
- [Langchain DataFrameLoader](https://python.langchain.com/api_reference/community/document_loaders/langchain_community.document_loaders.dataframe.DataFrameLoader.html#dataframeloader)
----

## Environment Setup

Set up the environment. You may refer to [Environment Setup](https://wikidocs.net/257836) for more details.

**[Note]**
- `langchain-opentutorial` is a package that provides a set of easy-to-use environment setup, useful functions and utilities for tutorials. 
- You can checkout the [`langchain-opentutorial`](https://github.com/LangChain-OpenTutorial/langchain-opentutorial-pypi) for more details.
- `unstructured` package is a Python library for extracting text and metadata from various document formats like PDF and CSV


```python
%%capture --no-stderr
%pip install langchain-opentutorial
```

```python
# Install required packages
from langchain_opentutorial import package

package.install(
    [
        "langchain_community",
        "unstructured"
    ],
    verbose=False,
    upgrade=False,
)
```

```python
# Set environment variables
from langchain_opentutorial import set_env

set_env(
    {
        "OPENAI_API_KEY": "",
        "LANGCHAIN_API_KEY": "",
        "LANGCHAIN_TRACING_V2": "true",
        "LANGCHAIN_ENDPOINT": "https://api.smith.langchain.com",
        "LANGCHAIN_PROJECT": "04-CSV-Loader",
    }
)
```

<pre class="custom">Environment variables have been set successfully.
</pre>

You can alternatively set `OPENAI_API_KEY` in `.env` file and load it. 

[Note] This is not necessary if you've already set `OPENAI_API_KEY` in previous steps.

```python
from dotenv import load_dotenv

load_dotenv()
```




<pre class="custom">True</pre>



## How to load CSVs

A comma-separated values (CSV) file is a delimited text file that uses a comma to separate values. LangChain can help you load CSV files easily—just import CSVLoader to get started. 

Each line of the file is a data record, and each record consists of one or more fields, separated by commas. 

We use a sample CSV file for the example.

```python
from langchain_community.document_loaders.csv_loader import CSVLoader

# Create CSVLoader instance
loader = CSVLoader(file_path="./data/titanic.csv")

# Load documents
docs = loader.load()

for record in docs[:2]:
    print(record)
```

<pre class="custom">page_content='PassengerId: 1
    Survived: 0
    Pclass: 3
    Name: Braund, Mr. Owen Harris
    Sex: male
    Age: 22
    SibSp: 1
    Parch: 0
    Ticket: A/5 21171
    Fare: 7.25
    Cabin: 
    Embarked: S' metadata={'source': './data/titanic.csv', 'row': 0}
    page_content='PassengerId: 2
    Survived: 1
    Pclass: 1
    Name: Cumings, Mrs. John Bradley (Florence Briggs Thayer)
    Sex: female
    Age: 38
    SibSp: 1
    Parch: 0
    Ticket: PC 17599
    Fare: 71.2833
    Cabin: C85
    Embarked: C' metadata={'source': './data/titanic.csv', 'row': 1}
</pre>

```python
print(docs[1].page_content)
```

<pre class="custom">PassengerId: 2
    Survived: 1
    Pclass: 1
    Name: Cumings, Mrs. John Bradley (Florence Briggs Thayer)
    Sex: female
    Age: 38
    SibSp: 1
    Parch: 0
    Ticket: PC 17599
    Fare: 71.2833
    Cabin: C85
    Embarked: C
</pre>

## Customizing the CSV parsing and loading

CSVLoader accept a csv_args keyword argument that supports customization of the parameters passed to Python's csv.DictReader. This allows you to handle various CSV formats, such as custom delimiters, quote characters, or specific newline handling. 

See Python's [csv module](https://docs.python.org/3/library/csv.html) documentation for more information on supported `csv_args` and how to tailor the parsing to your specific needs.

```python
loader = CSVLoader(
    file_path="./data/titanic.csv",
    csv_args={
        "delimiter": ",",
        "quotechar": '"',
        "fieldnames": [
            "Passenger ID",
            "Survival (1: Survived, 0: Died)",
            "Passenger Class",
            "Name",
            "Sex",
            "Age",
            "Number of Siblings/Spouses Aboard",
            "Number of Parents/Children Aboard",
            "Ticket Number",
            "Fare",
            "Cabin",
            "Port of Embarkation",
        ],
    },
)

docs = loader.load()

print(docs[1].page_content)
```

<pre class="custom">Passenger ID: 1
    Survival (1: Survived, 0: Died): 0
    Passenger Class: 3
    Name: Braund, Mr. Owen Harris
    Sex: male
    Age: 22
    Number of Siblings/Spouses Aboard: 1
    Number of Parents/Children Aboard: 0
    Ticket Number: A/5 21171
    Fare: 7.25
    Cabin: 
    Port of Embarkation: S
</pre>

## Specify a column to identify the document source

You should use the `source_column` argument to specify the source of the documents generated from each row. Otherwise `file_path` will be used as the source for all documents created from the CSV file.

This is particularly useful when using the documents loaded from a CSV file in a chain designed to answer questions based on their source.

```python
loader = CSVLoader(
    file_path="./data/titanic.csv",
    source_column="PassengerId",  # Specify the source column
)

docs = loader.load()  

print(docs[1])
print(docs[1].metadata)
```

<pre class="custom">page_content='PassengerId: 2
    Survived: 1
    Pclass: 1
    Name: Cumings, Mrs. John Bradley (Florence Briggs Thayer)
    Sex: female
    Age: 38
    SibSp: 1
    Parch: 0
    Ticket: PC 17599
    Fare: 71.2833
    Cabin: C85
    Embarked: C' metadata={'source': '2', 'row': 1}
    {'source': '2', 'row': 1}
</pre>

## Generating XML document format

This example shows how to generate XML Document format from CSVLoader. By processing data from a CSV file, you can convert its rows and columns into a structured XML representation.

Convert a row in the document.

```python
row = docs[1].page_content.split("\n")  # split by new line
row_str = "<row>"
for element in row:
    splitted_element = element.split(":")  # split by ":"
    value = splitted_element[-1]  # get value
    col = ":".join(splitted_element[:-1])  # get column name

    row_str += f"<{col}>{value.strip()}</{col}>"
row_str += "</row>"
print(row_str)
```

<pre class="custom"><row><PassengerId>2</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Cumings, Mrs. John Bradley (Florence Briggs Thayer)</Name><Sex>female</Sex><Age>38</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17599</Ticket><Fare>71.2833</Fare><Cabin>C85</Cabin><Embarked>C</Embarked></row>
</pre>

Convert entire rows in the document.

```python
for doc in docs[1:]:  # skip header
    row = doc.page_content.split("\n")
    row_str = "<row>"
    for element in row:
        splitted_element = element.split(":")  # split by ":"
        value = splitted_element[-1]  # get value
        col = ":".join(splitted_element[:-1])  # get column name
        row_str += f"<{col}>{value.strip()}</{col}>"
    row_str += "</row>"
    print(row_str)
```

<pre class="custom"><row><PassengerId>2</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Cumings, Mrs. John Bradley (Florence Briggs Thayer)</Name><Sex>female</Sex><Age>38</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17599</Ticket><Fare>71.2833</Fare><Cabin>C85</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>3</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Heikkinen, Miss. Laina</Name><Sex>female</Sex><Age>26</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O2. 3101282</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>4</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Futrelle, Mrs. Jacques Heath (Lily May Peel)</Name><Sex>female</Sex><Age>35</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>113803</Ticket><Fare>53.1</Fare><Cabin>C123</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>5</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Allen, Mr. William Henry</Name><Sex>male</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>373450</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>6</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Moran, Mr. James</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>330877</Ticket><Fare>8.4583</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>7</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>McCarthy, Mr. Timothy J</Name><Sex>male</Sex><Age>54</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>17463</Ticket><Fare>51.8625</Fare><Cabin>E46</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>8</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Palsson, Master. Gosta Leonard</Name><Sex>male</Sex><Age>2</Age><SibSp>3</SibSp><Parch>1</Parch><Ticket>349909</Ticket><Fare>21.075</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>9</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)</Name><Sex>female</Sex><Age>27</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>347742</Ticket><Fare>11.1333</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>10</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Nasser, Mrs. Nicholas (Adele Achem)</Name><Sex>female</Sex><Age>14</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>237736</Ticket><Fare>30.0708</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>11</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Sandstrom, Miss. Marguerite Rut</Name><Sex>female</Sex><Age>4</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>PP 9549</Ticket><Fare>16.7</Fare><Cabin>G6</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>12</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Bonnell, Miss. Elizabeth</Name><Sex>female</Sex><Age>58</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113783</Ticket><Fare>26.55</Fare><Cabin>C103</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>13</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Saundercock, Mr. William Henry</Name><Sex>male</Sex><Age>20</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/5. 2151</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>14</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Andersson, Mr. Anders Johan</Name><Sex>male</Sex><Age>39</Age><SibSp>1</SibSp><Parch>5</Parch><Ticket>347082</Ticket><Fare>31.275</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>15</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Vestrom, Miss. Hulda Amanda Adolfina</Name><Sex>female</Sex><Age>14</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350406</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>16</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Hewlett, Mrs. (Mary D Kingcome)</Name><Sex>female</Sex><Age>55</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>248706</Ticket><Fare>16</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>17</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rice, Master. Eugene</Name><Sex>male</Sex><Age>2</Age><SibSp>4</SibSp><Parch>1</Parch><Ticket>382652</Ticket><Fare>29.125</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>18</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Williams, Mr. Charles Eugene</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>244373</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>19</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Vander Planke, Mrs. Julius (Emelia Maria Vandemoortele)</Name><Sex>female</Sex><Age>31</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>345763</Ticket><Fare>18</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>20</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Masselmani, Mrs. Fatima</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2649</Ticket><Fare>7.225</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>21</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Fynney, Mr. Joseph J</Name><Sex>male</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>239865</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>22</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Beesley, Mr. Lawrence</Name><Sex>male</Sex><Age>34</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>248698</Ticket><Fare>13</Fare><Cabin>D56</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>23</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>McGowan, Miss. Anna "Annie"</Name><Sex>female</Sex><Age>15</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>330923</Ticket><Fare>8.0292</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>24</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Sloper, Mr. William Thompson</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113788</Ticket><Fare>35.5</Fare><Cabin>A6</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>25</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Palsson, Miss. Torborg Danira</Name><Sex>female</Sex><Age>8</Age><SibSp>3</SibSp><Parch>1</Parch><Ticket>349909</Ticket><Fare>21.075</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>26</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Asplund, Mrs. Carl Oscar (Selma Augusta Emilia Johansson)</Name><Sex>female</Sex><Age>38</Age><SibSp>1</SibSp><Parch>5</Parch><Ticket>347077</Ticket><Fare>31.3875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>27</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Emir, Mr. Farred Chehab</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2631</Ticket><Fare>7.225</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>28</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Fortune, Mr. Charles Alexander</Name><Sex>male</Sex><Age>19</Age><SibSp>3</SibSp><Parch>2</Parch><Ticket>19950</Ticket><Fare>263</Fare><Cabin>C23 C25 C27</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>29</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>O'Dwyer, Miss. Ellen "Nellie"</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>330959</Ticket><Fare>7.8792</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>30</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Todoroff, Mr. Lalio</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349216</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>31</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Uruchurtu, Don. Manuel E</Name><Sex>male</Sex><Age>40</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17601</Ticket><Fare>27.7208</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>32</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Spencer, Mrs. William Augustus (Marie Eugenie)</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17569</Ticket><Fare>146.5208</Fare><Cabin>B78</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>33</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Glynn, Miss. Mary Agatha</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>335677</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>34</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Wheadon, Mr. Edward H</Name><Sex>male</Sex><Age>66</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 24579</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>35</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Meyer, Mr. Edgar Joseph</Name><Sex>male</Sex><Age>28</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17604</Ticket><Fare>82.1708</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>36</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Holverson, Mr. Alexander Oskar</Name><Sex>male</Sex><Age>42</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>113789</Ticket><Fare>52</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>37</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Mamee, Mr. Hanna</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2677</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>38</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Cann, Mr. Ernest Charles</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A./5. 2152</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>39</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Vander Planke, Miss. Augusta Maria</Name><Sex>female</Sex><Age>18</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>345764</Ticket><Fare>18</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>40</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Nicola-Yarred, Miss. Jamila</Name><Sex>female</Sex><Age>14</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>2651</Ticket><Fare>11.2417</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>41</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ahlin, Mrs. Johan (Johanna Persdotter Larsson)</Name><Sex>female</Sex><Age>40</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>7546</Ticket><Fare>9.475</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>42</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Turpin, Mrs. William John Robert (Dorothy Ann Wonnacott)</Name><Sex>female</Sex><Age>27</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>11668</Ticket><Fare>21</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>43</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Kraeff, Mr. Theodor</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349253</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>44</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Laroche, Miss. Simonne Marie Anne Andree</Name><Sex>female</Sex><Age>3</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>SC/Paris 2123</Ticket><Fare>41.5792</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>45</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Devaney, Miss. Margaret Delia</Name><Sex>female</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>330958</Ticket><Fare>7.8792</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>46</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rogers, Mr. William John</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>S.C./A.4. 23567</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>47</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lennon, Mr. Denis</Name><Sex>male</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>370371</Ticket><Fare>15.5</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>48</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>O'Driscoll, Miss. Bridget</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>14311</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>49</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Samaan, Mr. Youssef</Name><Sex>male</Sex><Age></Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>2662</Ticket><Fare>21.6792</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>50</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Arnold-Franchi, Mrs. Josef (Josefine Franchi)</Name><Sex>female</Sex><Age>18</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>349237</Ticket><Fare>17.8</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>51</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Panula, Master. Juha Niilo</Name><Sex>male</Sex><Age>7</Age><SibSp>4</SibSp><Parch>1</Parch><Ticket>3101295</Ticket><Fare>39.6875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>52</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Nosworthy, Mr. Richard Cater</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/4. 39886</Ticket><Fare>7.8</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>53</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Harper, Mrs. Henry Sleeper (Myna Haxtun)</Name><Sex>female</Sex><Age>49</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17572</Ticket><Fare>76.7292</Fare><Cabin>D33</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>54</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Faunthorpe, Mrs. Lizzie (Elizabeth Anne Wilkinson)</Name><Sex>female</Sex><Age>29</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>2926</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>55</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Ostby, Mr. Engelhart Cornelius</Name><Sex>male</Sex><Age>65</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>113509</Ticket><Fare>61.9792</Fare><Cabin>B30</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>56</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Woolner, Mr. Hugh</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>19947</Ticket><Fare>35.5</Fare><Cabin>C52</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>57</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Rugg, Miss. Emily</Name><Sex>female</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 31026</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>58</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Novel, Mr. Mansouer</Name><Sex>male</Sex><Age>28.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2697</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>59</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>West, Miss. Constance Mirium</Name><Sex>female</Sex><Age>5</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>C.A. 34651</Ticket><Fare>27.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>60</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Goodwin, Master. William Frederick</Name><Sex>male</Sex><Age>11</Age><SibSp>5</SibSp><Parch>2</Parch><Ticket>CA 2144</Ticket><Fare>46.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>61</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sirayanian, Mr. Orsen</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2669</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>62</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Icard, Miss. Amelie</Name><Sex>female</Sex><Age>38</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113572</Ticket><Fare>80</Fare><Cabin>B28</Cabin><Embarked></Embarked></row>
    <row><PassengerId>63</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Harris, Mr. Henry Birkhardt</Name><Sex>male</Sex><Age>45</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>36973</Ticket><Fare>83.475</Fare><Cabin>C83</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>64</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Skoog, Master. Harald</Name><Sex>male</Sex><Age>4</Age><SibSp>3</SibSp><Parch>2</Parch><Ticket>347088</Ticket><Fare>27.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>65</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Stewart, Mr. Albert A</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17605</Ticket><Fare>27.7208</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>66</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Moubarek, Master. Gerios</Name><Sex>male</Sex><Age></Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>2661</Ticket><Fare>15.2458</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>67</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Nye, Mrs. (Elizabeth Ramell)</Name><Sex>female</Sex><Age>29</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 29395</Ticket><Fare>10.5</Fare><Cabin>F33</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>68</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Crease, Mr. Ernest James</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>S.P. 3464</Ticket><Fare>8.1583</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>69</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Andersson, Miss. Erna Alexandra</Name><Sex>female</Sex><Age>17</Age><SibSp>4</SibSp><Parch>2</Parch><Ticket>3101281</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>70</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Kink, Mr. Vincenz</Name><Sex>male</Sex><Age>26</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>315151</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>71</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Jenkin, Mr. Stephen Curnow</Name><Sex>male</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 33111</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>72</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Goodwin, Miss. Lillian Amy</Name><Sex>female</Sex><Age>16</Age><SibSp>5</SibSp><Parch>2</Parch><Ticket>CA 2144</Ticket><Fare>46.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>73</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Hood, Mr. Ambrose Jr</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>S.O.C. 14879</Ticket><Fare>73.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>74</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Chronopoulos, Mr. Apostolos</Name><Sex>male</Sex><Age>26</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>2680</Ticket><Fare>14.4542</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>75</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Bing, Mr. Lee</Name><Sex>male</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>1601</Ticket><Fare>56.4958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>76</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Moen, Mr. Sigurd Hansen</Name><Sex>male</Sex><Age>25</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>348123</Ticket><Fare>7.65</Fare><Cabin>F G73</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>77</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Staneff, Mr. Ivan</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349208</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>78</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Moutal, Mr. Rahamin Haim</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>374746</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>79</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Caldwell, Master. Alden Gates</Name><Sex>male</Sex><Age>0.83</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>248738</Ticket><Fare>29</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>80</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Dowdell, Miss. Elizabeth</Name><Sex>female</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>364516</Ticket><Fare>12.475</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>81</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Waelens, Mr. Achille</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>345767</Ticket><Fare>9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>82</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Sheerlinck, Mr. Jan Baptist</Name><Sex>male</Sex><Age>29</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>345779</Ticket><Fare>9.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>83</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>McDermott, Miss. Brigdet Delia</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>330932</Ticket><Fare>7.7875</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>84</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Carrau, Mr. Francisco M</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113059</Ticket><Fare>47.1</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>85</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Ilett, Miss. Bertha</Name><Sex>female</Sex><Age>17</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SO/C 14885</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>86</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Backstrom, Mrs. Karl Alfred (Maria Mathilda Gustafsson)</Name><Sex>female</Sex><Age>33</Age><SibSp>3</SibSp><Parch>0</Parch><Ticket>3101278</Ticket><Fare>15.85</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>87</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ford, Mr. William Neal</Name><Sex>male</Sex><Age>16</Age><SibSp>1</SibSp><Parch>3</Parch><Ticket>W./C. 6608</Ticket><Fare>34.375</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>88</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Slocovski, Mr. Selman Francis</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/OQ 392086</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>89</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Fortune, Miss. Mabel Helen</Name><Sex>female</Sex><Age>23</Age><SibSp>3</SibSp><Parch>2</Parch><Ticket>19950</Ticket><Fare>263</Fare><Cabin>C23 C25 C27</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>90</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Celotti, Mr. Francesco</Name><Sex>male</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>343275</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>91</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Christmann, Mr. Emil</Name><Sex>male</Sex><Age>29</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>343276</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>92</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Andreasson, Mr. Paul Edvin</Name><Sex>male</Sex><Age>20</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347466</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>93</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Chaffee, Mr. Herbert Fuller</Name><Sex>male</Sex><Age>46</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>W.E.P. 5734</Ticket><Fare>61.175</Fare><Cabin>E31</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>94</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Dean, Mr. Bertram Frank</Name><Sex>male</Sex><Age>26</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>C.A. 2315</Ticket><Fare>20.575</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>95</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Coxon, Mr. Daniel</Name><Sex>male</Sex><Age>59</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>364500</Ticket><Fare>7.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>96</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Shorney, Mr. Charles Joseph</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>374910</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>97</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Goldschmidt, Mr. George B</Name><Sex>male</Sex><Age>71</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17754</Ticket><Fare>34.6542</Fare><Cabin>A5</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>98</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Greenfield, Mr. William Bertram</Name><Sex>male</Sex><Age>23</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>PC 17759</Ticket><Fare>63.3583</Fare><Cabin>D10 D12</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>99</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Doling, Mrs. John T (Ada Julia Bone)</Name><Sex>female</Sex><Age>34</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>231919</Ticket><Fare>23</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>100</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Kantor, Mr. Sinai</Name><Sex>male</Sex><Age>34</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>244367</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>101</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Petranec, Miss. Matilda</Name><Sex>female</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349245</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>102</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Petroff, Mr. Pastcho ("Pentcho")</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349215</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>103</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>White, Mr. Richard Frasar</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>35281</Ticket><Fare>77.2875</Fare><Cabin>D26</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>104</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Johansson, Mr. Gustaf Joel</Name><Sex>male</Sex><Age>33</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>7540</Ticket><Fare>8.6542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>105</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Gustafsson, Mr. Anders Vilhelm</Name><Sex>male</Sex><Age>37</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>3101276</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>106</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Mionoff, Mr. Stoytcho</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349207</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>107</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Salkjelsvik, Miss. Anna Kristine</Name><Sex>female</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>343120</Ticket><Fare>7.65</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>108</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Moss, Mr. Albert Johan</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>312991</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>109</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rekic, Mr. Tido</Name><Sex>male</Sex><Age>38</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349249</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>110</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Moran, Miss. Bertha</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>371110</Ticket><Fare>24.15</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>111</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Porter, Mr. Walter Chamberlain</Name><Sex>male</Sex><Age>47</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>110465</Ticket><Fare>52</Fare><Cabin>C110</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>112</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Zabour, Miss. Hileni</Name><Sex>female</Sex><Age>14.5</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>2665</Ticket><Fare>14.4542</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>113</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Barton, Mr. David John</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>324669</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>114</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Jussila, Miss. Katriina</Name><Sex>female</Sex><Age>20</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>4136</Ticket><Fare>9.825</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>115</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Attalah, Miss. Malake</Name><Sex>female</Sex><Age>17</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2627</Ticket><Fare>14.4583</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>116</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Pekoniemi, Mr. Edvard</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O 2. 3101294</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>117</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Connors, Mr. Patrick</Name><Sex>male</Sex><Age>70.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>370369</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>118</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Turpin, Mr. William John Robert</Name><Sex>male</Sex><Age>29</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>11668</Ticket><Fare>21</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>119</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Baxter, Mr. Quigg Edmond</Name><Sex>male</Sex><Age>24</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>PC 17558</Ticket><Fare>247.5208</Fare><Cabin>B58 B60</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>120</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Andersson, Miss. Ellis Anna Maria</Name><Sex>female</Sex><Age>2</Age><SibSp>4</SibSp><Parch>2</Parch><Ticket>347082</Ticket><Fare>31.275</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>121</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Hickman, Mr. Stanley George</Name><Sex>male</Sex><Age>21</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>S.O.C. 14879</Ticket><Fare>73.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>122</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Moore, Mr. Leonard Charles</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A4. 54510</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>123</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Nasser, Mr. Nicholas</Name><Sex>male</Sex><Age>32.5</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>237736</Ticket><Fare>30.0708</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>124</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Webber, Miss. Susan</Name><Sex>female</Sex><Age>32.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>27267</Ticket><Fare>13</Fare><Cabin>E101</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>125</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>White, Mr. Percival Wayland</Name><Sex>male</Sex><Age>54</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>35281</Ticket><Fare>77.2875</Fare><Cabin>D26</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>126</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Nicola-Yarred, Master. Elias</Name><Sex>male</Sex><Age>12</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>2651</Ticket><Fare>11.2417</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>127</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>McMahon, Mr. Martin</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>370372</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>128</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Madsen, Mr. Fridtjof Arne</Name><Sex>male</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C 17369</Ticket><Fare>7.1417</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>129</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Peter, Miss. Anna</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>2668</Ticket><Fare>22.3583</Fare><Cabin>F E69</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>130</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ekstrom, Mr. Johan</Name><Sex>male</Sex><Age>45</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347061</Ticket><Fare>6.975</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>131</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Drazenoic, Mr. Jozef</Name><Sex>male</Sex><Age>33</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349241</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>132</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Coelho, Mr. Domingos Fernandeo</Name><Sex>male</Sex><Age>20</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/O.Q. 3101307</Ticket><Fare>7.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>133</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Robins, Mrs. Alexander A (Grace Charity Laury)</Name><Sex>female</Sex><Age>47</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>A/5. 3337</Ticket><Fare>14.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>134</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Weisz, Mrs. Leopold (Mathilde Francoise Pede)</Name><Sex>female</Sex><Age>29</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>228414</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>135</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Sobey, Mr. Samuel James Hayden</Name><Sex>male</Sex><Age>25</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 29178</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>136</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Richard, Mr. Emile</Name><Sex>male</Sex><Age>23</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SC/PARIS 2133</Ticket><Fare>15.0458</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>137</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Newsom, Miss. Helen Monypeny</Name><Sex>female</Sex><Age>19</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>11752</Ticket><Fare>26.2833</Fare><Cabin>D47</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>138</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Futrelle, Mr. Jacques Heath</Name><Sex>male</Sex><Age>37</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>113803</Ticket><Fare>53.1</Fare><Cabin>C123</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>139</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Osen, Mr. Olaf Elon</Name><Sex>male</Sex><Age>16</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>7534</Ticket><Fare>9.2167</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>140</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Giglio, Mr. Victor</Name><Sex>male</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17593</Ticket><Fare>79.2</Fare><Cabin>B86</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>141</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Boulos, Mrs. Joseph (Sultana)</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>2678</Ticket><Fare>15.2458</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>142</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Nysten, Miss. Anna Sofia</Name><Sex>female</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347081</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>143</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Hakkarainen, Mrs. Pekka Pietari (Elin Matilda Dolck)</Name><Sex>female</Sex><Age>24</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>STON/O2. 3101279</Ticket><Fare>15.85</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>144</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Burke, Mr. Jeremiah</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>365222</Ticket><Fare>6.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>145</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Andrew, Mr. Edgardo Samuel</Name><Sex>male</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>231945</Ticket><Fare>11.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>146</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Nicholls, Mr. Joseph Charles</Name><Sex>male</Sex><Age>19</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>C.A. 33112</Ticket><Fare>36.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>147</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Andersson, Mr. August Edvard ("Wennerstrom")</Name><Sex>male</Sex><Age>27</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350043</Ticket><Fare>7.7958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>148</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ford, Miss. Robina Maggie "Ruby"</Name><Sex>female</Sex><Age>9</Age><SibSp>2</SibSp><Parch>2</Parch><Ticket>W./C. 6608</Ticket><Fare>34.375</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>149</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Navratil, Mr. Michel ("Louis M Hoffman")</Name><Sex>male</Sex><Age>36.5</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>230080</Ticket><Fare>26</Fare><Cabin>F2</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>150</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Byles, Rev. Thomas Roussel Davids</Name><Sex>male</Sex><Age>42</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>244310</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>151</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Bateman, Rev. Robert James</Name><Sex>male</Sex><Age>51</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>S.O.P. 1166</Ticket><Fare>12.525</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>152</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Pears, Mrs. Thomas (Edith Wearne)</Name><Sex>female</Sex><Age>22</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>113776</Ticket><Fare>66.6</Fare><Cabin>C2</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>153</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Meo, Mr. Alfonzo</Name><Sex>male</Sex><Age>55.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A.5. 11206</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>154</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>van Billiard, Mr. Austin Blyler</Name><Sex>male</Sex><Age>40.5</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>A/5. 851</Ticket><Fare>14.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>155</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Olsen, Mr. Ole Martin</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>Fa 265302</Ticket><Fare>7.3125</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>156</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Williams, Mr. Charles Duane</Name><Sex>male</Sex><Age>51</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>PC 17597</Ticket><Fare>61.3792</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>157</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Gilnagh, Miss. Katherine "Katie"</Name><Sex>female</Sex><Age>16</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>35851</Ticket><Fare>7.7333</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>158</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Corn, Mr. Harry</Name><Sex>male</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/OQ 392090</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>159</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Smiljanic, Mr. Mile</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>315037</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>160</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sage, Master. Thomas Henry</Name><Sex>male</Sex><Age></Age><SibSp>8</SibSp><Parch>2</Parch><Ticket>CA. 2343</Ticket><Fare>69.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>161</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Cribb, Mr. John Hatfield</Name><Sex>male</Sex><Age>44</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>371362</Ticket><Fare>16.1</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>162</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Watt, Mrs. James (Elizabeth "Bessie" Inglis Milne)</Name><Sex>female</Sex><Age>40</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 33595</Ticket><Fare>15.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>163</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Bengtsson, Mr. John Viktor</Name><Sex>male</Sex><Age>26</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347068</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>164</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Calic, Mr. Jovo</Name><Sex>male</Sex><Age>17</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>315093</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>165</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Panula, Master. Eino Viljami</Name><Sex>male</Sex><Age>1</Age><SibSp>4</SibSp><Parch>1</Parch><Ticket>3101295</Ticket><Fare>39.6875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>166</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Goldsmith, Master. Frank John William "Frankie"</Name><Sex>male</Sex><Age>9</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>363291</Ticket><Fare>20.525</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>167</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Chibnall, Mrs. (Edith Martha Bowerman)</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>113505</Ticket><Fare>55</Fare><Cabin>E33</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>168</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Skoog, Mrs. William (Anna Bernhardina Karlsson)</Name><Sex>female</Sex><Age>45</Age><SibSp>1</SibSp><Parch>4</Parch><Ticket>347088</Ticket><Fare>27.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>169</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Baumann, Mr. John D</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17318</Ticket><Fare>25.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>170</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ling, Mr. Lee</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>1601</Ticket><Fare>56.4958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>171</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Van der hoef, Mr. Wyckoff</Name><Sex>male</Sex><Age>61</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>111240</Ticket><Fare>33.5</Fare><Cabin>B19</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>172</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rice, Master. Arthur</Name><Sex>male</Sex><Age>4</Age><SibSp>4</SibSp><Parch>1</Parch><Ticket>382652</Ticket><Fare>29.125</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>173</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Johnson, Miss. Eleanor Ileen</Name><Sex>female</Sex><Age>1</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>347742</Ticket><Fare>11.1333</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>174</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sivola, Mr. Antti Wilhelm</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O 2. 3101280</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>175</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Smith, Mr. James Clinch</Name><Sex>male</Sex><Age>56</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>17764</Ticket><Fare>30.6958</Fare><Cabin>A7</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>176</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Klasen, Mr. Klas Albin</Name><Sex>male</Sex><Age>18</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>350404</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>177</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lefebre, Master. Henry Forbes</Name><Sex>male</Sex><Age></Age><SibSp>3</SibSp><Parch>1</Parch><Ticket>4133</Ticket><Fare>25.4667</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>178</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Isham, Miss. Ann Elizabeth</Name><Sex>female</Sex><Age>50</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17595</Ticket><Fare>28.7125</Fare><Cabin>C49</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>179</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Hale, Mr. Reginald</Name><Sex>male</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>250653</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>180</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Leonard, Mr. Lionel</Name><Sex>male</Sex><Age>36</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>LINE</Ticket><Fare>0</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>181</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sage, Miss. Constance Gladys</Name><Sex>female</Sex><Age></Age><SibSp>8</SibSp><Parch>2</Parch><Ticket>CA. 2343</Ticket><Fare>69.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>182</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Pernot, Mr. Rene</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SC/PARIS 2131</Ticket><Fare>15.05</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>183</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Asplund, Master. Clarence Gustaf Hugo</Name><Sex>male</Sex><Age>9</Age><SibSp>4</SibSp><Parch>2</Parch><Ticket>347077</Ticket><Fare>31.3875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>184</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Becker, Master. Richard F</Name><Sex>male</Sex><Age>1</Age><SibSp>2</SibSp><Parch>1</Parch><Ticket>230136</Ticket><Fare>39</Fare><Cabin>F4</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>185</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Kink-Heilmann, Miss. Luise Gretchen</Name><Sex>female</Sex><Age>4</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>315153</Ticket><Fare>22.025</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>186</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Rood, Mr. Hugh Roscoe</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113767</Ticket><Fare>50</Fare><Cabin>A32</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>187</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>O'Brien, Mrs. Thomas (Johanna "Hannah" Godfrey)</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>370365</Ticket><Fare>15.5</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>188</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Romaine, Mr. Charles Hallace ("Mr C Rolmane")</Name><Sex>male</Sex><Age>45</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>111428</Ticket><Fare>26.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>189</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Bourke, Mr. John</Name><Sex>male</Sex><Age>40</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>364849</Ticket><Fare>15.5</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>190</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Turcin, Mr. Stjepan</Name><Sex>male</Sex><Age>36</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349247</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>191</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Pinsky, Mrs. (Rosa)</Name><Sex>female</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>234604</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>192</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Carbines, Mr. William</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>28424</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>193</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Andersen-Jensen, Miss. Carla Christine Nielsine</Name><Sex>female</Sex><Age>19</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>350046</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>194</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Navratil, Master. Michel M</Name><Sex>male</Sex><Age>3</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>230080</Ticket><Fare>26</Fare><Cabin>F2</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>195</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Brown, Mrs. James Joseph (Margaret Tobin)</Name><Sex>female</Sex><Age>44</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17610</Ticket><Fare>27.7208</Fare><Cabin>B4</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>196</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Lurette, Miss. Elise</Name><Sex>female</Sex><Age>58</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17569</Ticket><Fare>146.5208</Fare><Cabin>B80</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>197</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Mernagh, Mr. Robert</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>368703</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>198</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Olsen, Mr. Karl Siegwart Andreas</Name><Sex>male</Sex><Age>42</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>4579</Ticket><Fare>8.4042</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>199</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Madigan, Miss. Margaret "Maggie"</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>370370</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>200</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Yrois, Miss. Henriette ("Mrs Harbeck")</Name><Sex>female</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>248747</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>201</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Vande Walle, Mr. Nestor Cyriel</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>345770</Ticket><Fare>9.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>202</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sage, Mr. Frederick</Name><Sex>male</Sex><Age></Age><SibSp>8</SibSp><Parch>2</Parch><Ticket>CA. 2343</Ticket><Fare>69.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>203</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Johanson, Mr. Jakob Alfred</Name><Sex>male</Sex><Age>34</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>3101264</Ticket><Fare>6.4958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>204</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Youseff, Mr. Gerious</Name><Sex>male</Sex><Age>45.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2628</Ticket><Fare>7.225</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>205</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Cohen, Mr. Gurshon "Gus"</Name><Sex>male</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/5 3540</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>206</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Strom, Miss. Telma Matilda</Name><Sex>female</Sex><Age>2</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>347054</Ticket><Fare>10.4625</Fare><Cabin>G6</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>207</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Backstrom, Mr. Karl Alfred</Name><Sex>male</Sex><Age>32</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>3101278</Ticket><Fare>15.85</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>208</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Albimona, Mr. Nassef Cassem</Name><Sex>male</Sex><Age>26</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2699</Ticket><Fare>18.7875</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>209</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Carr, Miss. Helen "Ellen"</Name><Sex>female</Sex><Age>16</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>367231</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>210</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Blank, Mr. Henry</Name><Sex>male</Sex><Age>40</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>112277</Ticket><Fare>31</Fare><Cabin>A31</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>211</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ali, Mr. Ahmed</Name><Sex>male</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/O.Q. 3101311</Ticket><Fare>7.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>212</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Cameron, Miss. Clear Annie</Name><Sex>female</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>F.C.C. 13528</Ticket><Fare>21</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>213</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Perkin, Mr. John Henry</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/5 21174</Ticket><Fare>7.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>214</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Givard, Mr. Hans Kristensen</Name><Sex>male</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>250646</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>215</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Kiernan, Mr. Philip</Name><Sex>male</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>367229</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>216</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Newell, Miss. Madeleine</Name><Sex>female</Sex><Age>31</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>35273</Ticket><Fare>113.275</Fare><Cabin>D36</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>217</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Honkanen, Miss. Eliina</Name><Sex>female</Sex><Age>27</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O2. 3101283</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>218</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Jacobsohn, Mr. Sidney Samuel</Name><Sex>male</Sex><Age>42</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>243847</Ticket><Fare>27</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>219</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Bazzani, Miss. Albina</Name><Sex>female</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>11813</Ticket><Fare>76.2917</Fare><Cabin>D15</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>220</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Harris, Mr. Walter</Name><Sex>male</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>W/C 14208</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>221</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Sunderland, Mr. Victor Francis</Name><Sex>male</Sex><Age>16</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/OQ 392089</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>222</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Bracken, Mr. James H</Name><Sex>male</Sex><Age>27</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>220367</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>223</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Green, Mr. George Henry</Name><Sex>male</Sex><Age>51</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>21440</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>224</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Nenkoff, Mr. Christo</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349234</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>225</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Hoyt, Mr. Frederick Maxfield</Name><Sex>male</Sex><Age>38</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>19943</Ticket><Fare>90</Fare><Cabin>C93</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>226</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Berglund, Mr. Karl Ivar Sven</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PP 4348</Ticket><Fare>9.35</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>227</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Mellors, Mr. William John</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SW/PP 751</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>228</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lovell, Mr. John Hall ("Henry")</Name><Sex>male</Sex><Age>20.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/5 21173</Ticket><Fare>7.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>229</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Fahlstrom, Mr. Arne Jonas</Name><Sex>male</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>236171</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>230</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lefebre, Miss. Mathilde</Name><Sex>female</Sex><Age></Age><SibSp>3</SibSp><Parch>1</Parch><Ticket>4133</Ticket><Fare>25.4667</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>231</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Harris, Mrs. Henry Birkhardt (Irene Wallach)</Name><Sex>female</Sex><Age>35</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>36973</Ticket><Fare>83.475</Fare><Cabin>C83</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>232</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Larsson, Mr. Bengt Edvin</Name><Sex>male</Sex><Age>29</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347067</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>233</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Sjostedt, Mr. Ernst Adolf</Name><Sex>male</Sex><Age>59</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>237442</Ticket><Fare>13.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>234</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Asplund, Miss. Lillian Gertrud</Name><Sex>female</Sex><Age>5</Age><SibSp>4</SibSp><Parch>2</Parch><Ticket>347077</Ticket><Fare>31.3875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>235</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Leyson, Mr. Robert William Norman</Name><Sex>male</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 29566</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>236</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Harknett, Miss. Alice Phoebe</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>W./C. 6609</Ticket><Fare>7.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>237</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Hold, Mr. Stephen</Name><Sex>male</Sex><Age>44</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>26707</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>238</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Collyer, Miss. Marjorie "Lottie"</Name><Sex>female</Sex><Age>8</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>C.A. 31921</Ticket><Fare>26.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>239</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Pengelly, Mr. Frederick William</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>28665</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>240</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Hunt, Mr. George Henry</Name><Sex>male</Sex><Age>33</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SCO/W 1585</Ticket><Fare>12.275</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>241</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Zabour, Miss. Thamine</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>2665</Ticket><Fare>14.4542</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>242</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Murphy, Miss. Katherine "Kate"</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>367230</Ticket><Fare>15.5</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>243</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Coleridge, Mr. Reginald Charles</Name><Sex>male</Sex><Age>29</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>W./C. 14263</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>244</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Maenpaa, Mr. Matti Alexanteri</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O 2. 3101275</Ticket><Fare>7.125</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>245</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Attalah, Mr. Sleiman</Name><Sex>male</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2694</Ticket><Fare>7.225</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>246</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Minahan, Dr. William Edward</Name><Sex>male</Sex><Age>44</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>19928</Ticket><Fare>90</Fare><Cabin>C78</Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>247</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lindahl, Miss. Agda Thorilda Viktoria</Name><Sex>female</Sex><Age>25</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347071</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>248</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Hamalainen, Mrs. William (Anna)</Name><Sex>female</Sex><Age>24</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>250649</Ticket><Fare>14.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>249</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Beckwith, Mr. Richard Leonard</Name><Sex>male</Sex><Age>37</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>11751</Ticket><Fare>52.5542</Fare><Cabin>D35</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>250</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Carter, Rev. Ernest Courtenay</Name><Sex>male</Sex><Age>54</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>244252</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>251</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Reed, Mr. James George</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>362316</Ticket><Fare>7.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>252</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Strom, Mrs. Wilhelm (Elna Matilda Persson)</Name><Sex>female</Sex><Age>29</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>347054</Ticket><Fare>10.4625</Fare><Cabin>G6</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>253</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Stead, Mr. William Thomas</Name><Sex>male</Sex><Age>62</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113514</Ticket><Fare>26.55</Fare><Cabin>C87</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>254</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lobb, Mr. William Arthur</Name><Sex>male</Sex><Age>30</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>A/5. 3336</Ticket><Fare>16.1</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>255</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rosblom, Mrs. Viktor (Helena Wilhelmina)</Name><Sex>female</Sex><Age>41</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>370129</Ticket><Fare>20.2125</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>256</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Touma, Mrs. Darwis (Hanne Youssef Razi)</Name><Sex>female</Sex><Age>29</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>2650</Ticket><Fare>15.2458</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>257</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Thorne, Mrs. Gertrude Maybelle</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17585</Ticket><Fare>79.2</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>258</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Cherry, Miss. Gladys</Name><Sex>female</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>110152</Ticket><Fare>86.5</Fare><Cabin>B77</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>259</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Ward, Miss. Anna</Name><Sex>female</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17755</Ticket><Fare>512.3292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>260</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Parrish, Mrs. (Lutie Davis)</Name><Sex>female</Sex><Age>50</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>230433</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>261</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Smith, Mr. Thomas</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>384461</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>262</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Asplund, Master. Edvin Rojj Felix</Name><Sex>male</Sex><Age>3</Age><SibSp>4</SibSp><Parch>2</Parch><Ticket>347077</Ticket><Fare>31.3875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>263</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Taussig, Mr. Emil</Name><Sex>male</Sex><Age>52</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>110413</Ticket><Fare>79.65</Fare><Cabin>E67</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>264</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Harrison, Mr. William</Name><Sex>male</Sex><Age>40</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>112059</Ticket><Fare>0</Fare><Cabin>B94</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>265</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Henry, Miss. Delia</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>382649</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>266</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Reeves, Mr. David</Name><Sex>male</Sex><Age>36</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 17248</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>267</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Panula, Mr. Ernesti Arvid</Name><Sex>male</Sex><Age>16</Age><SibSp>4</SibSp><Parch>1</Parch><Ticket>3101295</Ticket><Fare>39.6875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>268</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Persson, Mr. Ernst Ulrik</Name><Sex>male</Sex><Age>25</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>347083</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>269</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Graham, Mrs. William Thompson (Edith Junkins)</Name><Sex>female</Sex><Age>58</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>PC 17582</Ticket><Fare>153.4625</Fare><Cabin>C125</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>270</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Bissette, Miss. Amelia</Name><Sex>female</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17760</Ticket><Fare>135.6333</Fare><Cabin>C99</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>271</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Cairns, Mr. Alexander</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113798</Ticket><Fare>31</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>272</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Tornquist, Mr. William Henry</Name><Sex>male</Sex><Age>25</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>LINE</Ticket><Fare>0</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>273</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Mellinger, Mrs. (Elizabeth Anne Maidment)</Name><Sex>female</Sex><Age>41</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>250644</Ticket><Fare>19.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>274</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Natsch, Mr. Charles H</Name><Sex>male</Sex><Age>37</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>PC 17596</Ticket><Fare>29.7</Fare><Cabin>C118</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>275</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Healy, Miss. Hanora "Nora"</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>370375</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>276</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Andrews, Miss. Kornelia Theodosia</Name><Sex>female</Sex><Age>63</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>13502</Ticket><Fare>77.9583</Fare><Cabin>D7</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>277</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lindblom, Miss. Augusta Charlotta</Name><Sex>female</Sex><Age>45</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347073</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>278</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Parkes, Mr. Francis "Frank"</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>239853</Ticket><Fare>0</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>279</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rice, Master. Eric</Name><Sex>male</Sex><Age>7</Age><SibSp>4</SibSp><Parch>1</Parch><Ticket>382652</Ticket><Fare>29.125</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>280</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Abbott, Mrs. Stanton (Rosa Hunt)</Name><Sex>female</Sex><Age>35</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>C.A. 2673</Ticket><Fare>20.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>281</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Duane, Mr. Frank</Name><Sex>male</Sex><Age>65</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>336439</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>282</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Olsson, Mr. Nils Johan Goransson</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347464</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>283</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>de Pelsmaeker, Mr. Alfons</Name><Sex>male</Sex><Age>16</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>345778</Ticket><Fare>9.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>284</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Dorking, Mr. Edward Arthur</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/5. 10482</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>285</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Smith, Mr. Richard William</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113056</Ticket><Fare>26</Fare><Cabin>A19</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>286</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Stankovic, Mr. Ivan</Name><Sex>male</Sex><Age>33</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349239</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>287</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>de Mulder, Mr. Theodore</Name><Sex>male</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>345774</Ticket><Fare>9.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>288</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Naidenoff, Mr. Penko</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349206</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>289</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Hosono, Mr. Masabumi</Name><Sex>male</Sex><Age>42</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>237798</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>290</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Connolly, Miss. Kate</Name><Sex>female</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>370373</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>291</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Barber, Miss. Ellen "Nellie"</Name><Sex>female</Sex><Age>26</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>19877</Ticket><Fare>78.85</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>292</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Bishop, Mrs. Dickinson H (Helen Walton)</Name><Sex>female</Sex><Age>19</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>11967</Ticket><Fare>91.0792</Fare><Cabin>B49</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>293</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Levy, Mr. Rene Jacques</Name><Sex>male</Sex><Age>36</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SC/Paris 2163</Ticket><Fare>12.875</Fare><Cabin>D</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>294</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Haas, Miss. Aloisia</Name><Sex>female</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349236</Ticket><Fare>8.85</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>295</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Mineff, Mr. Ivan</Name><Sex>male</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349233</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>296</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Lewy, Mr. Ervin G</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17612</Ticket><Fare>27.7208</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>297</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Hanna, Mr. Mansour</Name><Sex>male</Sex><Age>23.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2693</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>298</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Allison, Miss. Helen Loraine</Name><Sex>female</Sex><Age>2</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>113781</Ticket><Fare>151.55</Fare><Cabin>C22 C26</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>299</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Saalfeld, Mr. Adolphe</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>19988</Ticket><Fare>30.5</Fare><Cabin>C106</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>300</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Baxter, Mrs. James (Helene DeLaudeniere Chaput)</Name><Sex>female</Sex><Age>50</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>PC 17558</Ticket><Fare>247.5208</Fare><Cabin>B58 B60</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>301</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Kelly, Miss. Anna Katherine "Annie Kate"</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>9234</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>302</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>McCoy, Mr. Bernard</Name><Sex>male</Sex><Age></Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>367226</Ticket><Fare>23.25</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>303</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Johnson, Mr. William Cahoone Jr</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>LINE</Ticket><Fare>0</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>304</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Keane, Miss. Nora A</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>226593</Ticket><Fare>12.35</Fare><Cabin>E101</Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>305</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Williams, Mr. Howard Hugh "Harry"</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/5 2466</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>306</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Allison, Master. Hudson Trevor</Name><Sex>male</Sex><Age>0.92</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>113781</Ticket><Fare>151.55</Fare><Cabin>C22 C26</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>307</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Fleming, Miss. Margaret</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>17421</Ticket><Fare>110.8833</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>308</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Penasco y Castellana, Mrs. Victor de Satode (Maria Josefa Perez de Soto y Vallejo)</Name><Sex>female</Sex><Age>17</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17758</Ticket><Fare>108.9</Fare><Cabin>C65</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>309</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Abelson, Mr. Samuel</Name><Sex>male</Sex><Age>30</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>P/PP 3381</Ticket><Fare>24</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>310</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Francatelli, Miss. Laura Mabel</Name><Sex>female</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17485</Ticket><Fare>56.9292</Fare><Cabin>E36</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>311</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Hays, Miss. Margaret Bechstein</Name><Sex>female</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>11767</Ticket><Fare>83.1583</Fare><Cabin>C54</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>312</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Ryerson, Miss. Emily Borie</Name><Sex>female</Sex><Age>18</Age><SibSp>2</SibSp><Parch>2</Parch><Ticket>PC 17608</Ticket><Fare>262.375</Fare><Cabin>B57 B59 B63 B66</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>313</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Lahtinen, Mrs. William (Anna Sylfven)</Name><Sex>female</Sex><Age>26</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>250651</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>314</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Hendekovic, Mr. Ignjac</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349243</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>315</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Hart, Mr. Benjamin</Name><Sex>male</Sex><Age>43</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>F.C.C. 13529</Ticket><Fare>26.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>316</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Nilsson, Miss. Helmina Josefina</Name><Sex>female</Sex><Age>26</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347470</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>317</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Kantor, Mrs. Sinai (Miriam Sternin)</Name><Sex>female</Sex><Age>24</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>244367</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>318</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Moraweck, Dr. Ernest</Name><Sex>male</Sex><Age>54</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>29011</Ticket><Fare>14</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>319</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Wick, Miss. Mary Natalie</Name><Sex>female</Sex><Age>31</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>36928</Ticket><Fare>164.8667</Fare><Cabin>C7</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>320</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Spedden, Mrs. Frederic Oakley (Margaretta Corning Stone)</Name><Sex>female</Sex><Age>40</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>16966</Ticket><Fare>134.5</Fare><Cabin>E34</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>321</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Dennis, Mr. Samuel</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/5 21172</Ticket><Fare>7.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>322</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Danoff, Mr. Yoto</Name><Sex>male</Sex><Age>27</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349219</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>323</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Slayter, Miss. Hilda Mary</Name><Sex>female</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>234818</Ticket><Fare>12.35</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>324</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Caldwell, Mrs. Albert Francis (Sylvia Mae Harbaugh)</Name><Sex>female</Sex><Age>22</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>248738</Ticket><Fare>29</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>325</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sage, Mr. George John Jr</Name><Sex>male</Sex><Age></Age><SibSp>8</SibSp><Parch>2</Parch><Ticket>CA. 2343</Ticket><Fare>69.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>326</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Young, Miss. Marie Grice</Name><Sex>female</Sex><Age>36</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17760</Ticket><Fare>135.6333</Fare><Cabin>C32</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>327</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Nysveen, Mr. Johan Hansen</Name><Sex>male</Sex><Age>61</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>345364</Ticket><Fare>6.2375</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>328</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Ball, Mrs. (Ada E Hall)</Name><Sex>female</Sex><Age>36</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>28551</Ticket><Fare>13</Fare><Cabin>D</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>329</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Goldsmith, Mrs. Frank John (Emily Alice Brown)</Name><Sex>female</Sex><Age>31</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>363291</Ticket><Fare>20.525</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>330</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Hippach, Miss. Jean Gertrude</Name><Sex>female</Sex><Age>16</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>111361</Ticket><Fare>57.9792</Fare><Cabin>B18</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>331</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>McCoy, Miss. Agnes</Name><Sex>female</Sex><Age></Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>367226</Ticket><Fare>23.25</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>332</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Partner, Mr. Austen</Name><Sex>male</Sex><Age>45.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113043</Ticket><Fare>28.5</Fare><Cabin>C124</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>333</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Graham, Mr. George Edward</Name><Sex>male</Sex><Age>38</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>PC 17582</Ticket><Fare>153.4625</Fare><Cabin>C91</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>334</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Vander Planke, Mr. Leo Edmondus</Name><Sex>male</Sex><Age>16</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>345764</Ticket><Fare>18</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>335</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Frauenthal, Mrs. Henry William (Clara Heinsheimer)</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17611</Ticket><Fare>133.65</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>336</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Denkoff, Mr. Mitto</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349225</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>337</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Pears, Mr. Thomas Clinton</Name><Sex>male</Sex><Age>29</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>113776</Ticket><Fare>66.6</Fare><Cabin>C2</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>338</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Burns, Miss. Elizabeth Margaret</Name><Sex>female</Sex><Age>41</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>16966</Ticket><Fare>134.5</Fare><Cabin>E40</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>339</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Dahl, Mr. Karl Edwart</Name><Sex>male</Sex><Age>45</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>7598</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>340</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Blackwell, Mr. Stephen Weart</Name><Sex>male</Sex><Age>45</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113784</Ticket><Fare>35.5</Fare><Cabin>T</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>341</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Navratil, Master. Edmond Roger</Name><Sex>male</Sex><Age>2</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>230080</Ticket><Fare>26</Fare><Cabin>F2</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>342</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Fortune, Miss. Alice Elizabeth</Name><Sex>female</Sex><Age>24</Age><SibSp>3</SibSp><Parch>2</Parch><Ticket>19950</Ticket><Fare>263</Fare><Cabin>C23 C25 C27</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>343</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Collander, Mr. Erik Gustaf</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>248740</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>344</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Sedgwick, Mr. Charles Frederick Waddington</Name><Sex>male</Sex><Age>25</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>244361</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>345</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Fox, Mr. Stanley Hubert</Name><Sex>male</Sex><Age>36</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>229236</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>346</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Brown, Miss. Amelia "Mildred"</Name><Sex>female</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>248733</Ticket><Fare>13</Fare><Cabin>F33</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>347</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Smith, Miss. Marion Elsie</Name><Sex>female</Sex><Age>40</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>31418</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>348</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Davison, Mrs. Thomas Henry (Mary E Finck)</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>386525</Ticket><Fare>16.1</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>349</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Coutts, Master. William Loch "William"</Name><Sex>male</Sex><Age>3</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>C.A. 37671</Ticket><Fare>15.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>350</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Dimic, Mr. Jovan</Name><Sex>male</Sex><Age>42</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>315088</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>351</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Odahl, Mr. Nils Martin</Name><Sex>male</Sex><Age>23</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>7267</Ticket><Fare>9.225</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>352</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Williams-Lambert, Mr. Fletcher Fellows</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113510</Ticket><Fare>35</Fare><Cabin>C128</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>353</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Elias, Mr. Tannous</Name><Sex>male</Sex><Age>15</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>2695</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>354</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Arnold-Franchi, Mr. Josef</Name><Sex>male</Sex><Age>25</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>349237</Ticket><Fare>17.8</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>355</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Yousif, Mr. Wazli</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2647</Ticket><Fare>7.225</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>356</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Vanden Steen, Mr. Leo Peter</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>345783</Ticket><Fare>9.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>357</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Bowerman, Miss. Elsie Edith</Name><Sex>female</Sex><Age>22</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>113505</Ticket><Fare>55</Fare><Cabin>E33</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>358</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Funk, Miss. Annie Clemmer</Name><Sex>female</Sex><Age>38</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>237671</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>359</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>McGovern, Miss. Mary</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>330931</Ticket><Fare>7.8792</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>360</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Mockler, Miss. Helen Mary "Ellie"</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>330980</Ticket><Fare>7.8792</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>361</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Skoog, Mr. Wilhelm</Name><Sex>male</Sex><Age>40</Age><SibSp>1</SibSp><Parch>4</Parch><Ticket>347088</Ticket><Fare>27.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>362</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>del Carlo, Mr. Sebastiano</Name><Sex>male</Sex><Age>29</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>SC/PARIS 2167</Ticket><Fare>27.7208</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>363</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Barbara, Mrs. (Catherine David)</Name><Sex>female</Sex><Age>45</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>2691</Ticket><Fare>14.4542</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>364</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Asim, Mr. Adola</Name><Sex>male</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/O.Q. 3101310</Ticket><Fare>7.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>365</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>O'Brien, Mr. Thomas</Name><Sex>male</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>370365</Ticket><Fare>15.5</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>366</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Adahl, Mr. Mauritz Nils Martin</Name><Sex>male</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C 7076</Ticket><Fare>7.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>367</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Warren, Mrs. Frank Manley (Anna Sophia Atkinson)</Name><Sex>female</Sex><Age>60</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>110813</Ticket><Fare>75.25</Fare><Cabin>D37</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>368</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Moussa, Mrs. (Mantoura Boulos)</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2626</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>369</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Jermyn, Miss. Annie</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>14313</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>370</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Aubart, Mme. Leontine Pauline</Name><Sex>female</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17477</Ticket><Fare>69.3</Fare><Cabin>B35</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>371</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Harder, Mr. George Achilles</Name><Sex>male</Sex><Age>25</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>11765</Ticket><Fare>55.4417</Fare><Cabin>E50</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>372</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Wiklund, Mr. Jakob Alfred</Name><Sex>male</Sex><Age>18</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>3101267</Ticket><Fare>6.4958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>373</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Beavan, Mr. William Thomas</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>323951</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>374</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Ringhini, Mr. Sante</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17760</Ticket><Fare>135.6333</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>375</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Palsson, Miss. Stina Viola</Name><Sex>female</Sex><Age>3</Age><SibSp>3</SibSp><Parch>1</Parch><Ticket>349909</Ticket><Fare>21.075</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>376</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Meyer, Mrs. Edgar Joseph (Leila Saks)</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17604</Ticket><Fare>82.1708</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>377</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Landergren, Miss. Aurora Adelia</Name><Sex>female</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C 7077</Ticket><Fare>7.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>378</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Widener, Mr. Harry Elkins</Name><Sex>male</Sex><Age>27</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>113503</Ticket><Fare>211.5</Fare><Cabin>C82</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>379</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Betros, Mr. Tannous</Name><Sex>male</Sex><Age>20</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2648</Ticket><Fare>4.0125</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>380</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Gustafsson, Mr. Karl Gideon</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347069</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>381</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Bidois, Miss. Rosalie</Name><Sex>female</Sex><Age>42</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17757</Ticket><Fare>227.525</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>382</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Nakid, Miss. Maria ("Mary")</Name><Sex>female</Sex><Age>1</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>2653</Ticket><Fare>15.7417</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>383</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Tikkanen, Mr. Juho</Name><Sex>male</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O 2. 3101293</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>384</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Holverson, Mrs. Alexander Oskar (Mary Aline Towner)</Name><Sex>female</Sex><Age>35</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>113789</Ticket><Fare>52</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>385</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Plotcharsky, Mr. Vasil</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349227</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>386</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Davies, Mr. Charles Henry</Name><Sex>male</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>S.O.C. 14879</Ticket><Fare>73.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>387</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Goodwin, Master. Sidney Leonard</Name><Sex>male</Sex><Age>1</Age><SibSp>5</SibSp><Parch>2</Parch><Ticket>CA 2144</Ticket><Fare>46.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>388</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Buss, Miss. Kate</Name><Sex>female</Sex><Age>36</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>27849</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>389</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sadlier, Mr. Matthew</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>367655</Ticket><Fare>7.7292</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>390</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Lehmann, Miss. Bertha</Name><Sex>female</Sex><Age>17</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SC 1748</Ticket><Fare>12</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>391</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Carter, Mr. William Ernest</Name><Sex>male</Sex><Age>36</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>113760</Ticket><Fare>120</Fare><Cabin>B96 B98</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>392</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Jansson, Mr. Carl Olof</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350034</Ticket><Fare>7.7958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>393</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Gustafsson, Mr. Johan Birger</Name><Sex>male</Sex><Age>28</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>3101277</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>394</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Newell, Miss. Marjorie</Name><Sex>female</Sex><Age>23</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>35273</Ticket><Fare>113.275</Fare><Cabin>D36</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>395</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Sandstrom, Mrs. Hjalmar (Agnes Charlotta Bengtsson)</Name><Sex>female</Sex><Age>24</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>PP 9549</Ticket><Fare>16.7</Fare><Cabin>G6</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>396</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Johansson, Mr. Erik</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350052</Ticket><Fare>7.7958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>397</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Olsson, Miss. Elina</Name><Sex>female</Sex><Age>31</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350407</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>398</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>McKane, Mr. Peter David</Name><Sex>male</Sex><Age>46</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>28403</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>399</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Pain, Dr. Alfred</Name><Sex>male</Sex><Age>23</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>244278</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>400</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Trout, Mrs. William H (Jessie L)</Name><Sex>female</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>240929</Ticket><Fare>12.65</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>401</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Niskanen, Mr. Juha</Name><Sex>male</Sex><Age>39</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O 2. 3101289</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>402</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Adams, Mr. John</Name><Sex>male</Sex><Age>26</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>341826</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>403</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Jussila, Miss. Mari Aina</Name><Sex>female</Sex><Age>21</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>4137</Ticket><Fare>9.825</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>404</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Hakkarainen, Mr. Pekka Pietari</Name><Sex>male</Sex><Age>28</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>STON/O2. 3101279</Ticket><Fare>15.85</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>405</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Oreskovic, Miss. Marija</Name><Sex>female</Sex><Age>20</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>315096</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>406</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Gale, Mr. Shadrach</Name><Sex>male</Sex><Age>34</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>28664</Ticket><Fare>21</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>407</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Widegren, Mr. Carl/Charles Peter</Name><Sex>male</Sex><Age>51</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347064</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>408</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Richards, Master. William Rowe</Name><Sex>male</Sex><Age>3</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>29106</Ticket><Fare>18.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>409</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Birkeland, Mr. Hans Martin Monsen</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>312992</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>410</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lefebre, Miss. Ida</Name><Sex>female</Sex><Age></Age><SibSp>3</SibSp><Parch>1</Parch><Ticket>4133</Ticket><Fare>25.4667</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>411</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sdycoff, Mr. Todor</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349222</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>412</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Hart, Mr. Henry</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>394140</Ticket><Fare>6.8583</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>413</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Minahan, Miss. Daisy E</Name><Sex>female</Sex><Age>33</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>19928</Ticket><Fare>90</Fare><Cabin>C78</Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>414</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Cunningham, Mr. Alfred Fleming</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>239853</Ticket><Fare>0</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>415</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Sundman, Mr. Johan Julian</Name><Sex>male</Sex><Age>44</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O 2. 3101269</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>416</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Meek, Mrs. Thomas (Annie Louise Rowley)</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>343095</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>417</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Drew, Mrs. James Vivian (Lulu Thorne Christian)</Name><Sex>female</Sex><Age>34</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>28220</Ticket><Fare>32.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>418</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Silven, Miss. Lyyli Karoliina</Name><Sex>female</Sex><Age>18</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>250652</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>419</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Matthews, Mr. William John</Name><Sex>male</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>28228</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>420</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Van Impe, Miss. Catharina</Name><Sex>female</Sex><Age>10</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>345773</Ticket><Fare>24.15</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>421</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Gheorgheff, Mr. Stanio</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349254</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>422</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Charters, Mr. David</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/5. 13032</Ticket><Fare>7.7333</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>423</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Zimmerman, Mr. Leo</Name><Sex>male</Sex><Age>29</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>315082</Ticket><Fare>7.875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>424</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Danbom, Mrs. Ernst Gilbert (Anna Sigrid Maria Brogren)</Name><Sex>female</Sex><Age>28</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>347080</Ticket><Fare>14.4</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>425</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rosblom, Mr. Viktor Richard</Name><Sex>male</Sex><Age>18</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>370129</Ticket><Fare>20.2125</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>426</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Wiseman, Mr. Phillippe</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/4. 34244</Ticket><Fare>7.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>427</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Clarke, Mrs. Charles V (Ada Maria Winfield)</Name><Sex>female</Sex><Age>28</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>2003</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>428</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Phillips, Miss. Kate Florence ("Mrs Kate Louise Phillips Marshall")</Name><Sex>female</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>250655</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>429</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Flynn, Mr. James</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>364851</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>430</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Pickard, Mr. Berk (Berk Trembisky)</Name><Sex>male</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/O.Q. 392078</Ticket><Fare>8.05</Fare><Cabin>E10</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>431</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Bjornstrom-Steffansson, Mr. Mauritz Hakan</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>110564</Ticket><Fare>26.55</Fare><Cabin>C52</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>432</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Thorneycroft, Mrs. Percival (Florence Kate White)</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>376564</Ticket><Fare>16.1</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>433</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Louch, Mrs. Charles Alexander (Alice Adelaide Slow)</Name><Sex>female</Sex><Age>42</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>SC/AH 3085</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>434</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Kallio, Mr. Nikolai Erland</Name><Sex>male</Sex><Age>17</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O 2. 3101274</Ticket><Fare>7.125</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>435</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Silvey, Mr. William Baird</Name><Sex>male</Sex><Age>50</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>13507</Ticket><Fare>55.9</Fare><Cabin>E44</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>436</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Carter, Miss. Lucile Polk</Name><Sex>female</Sex><Age>14</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>113760</Ticket><Fare>120</Fare><Cabin>B96 B98</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>437</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ford, Miss. Doolina Margaret "Daisy"</Name><Sex>female</Sex><Age>21</Age><SibSp>2</SibSp><Parch>2</Parch><Ticket>W./C. 6608</Ticket><Fare>34.375</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>438</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Richards, Mrs. Sidney (Emily Hocking)</Name><Sex>female</Sex><Age>24</Age><SibSp>2</SibSp><Parch>3</Parch><Ticket>29106</Ticket><Fare>18.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>439</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Fortune, Mr. Mark</Name><Sex>male</Sex><Age>64</Age><SibSp>1</SibSp><Parch>4</Parch><Ticket>19950</Ticket><Fare>263</Fare><Cabin>C23 C25 C27</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>440</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Kvillner, Mr. Johan Henrik Johannesson</Name><Sex>male</Sex><Age>31</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 18723</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>441</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Hart, Mrs. Benjamin (Esther Ada Bloomfield)</Name><Sex>female</Sex><Age>45</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>F.C.C. 13529</Ticket><Fare>26.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>442</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Hampe, Mr. Leon</Name><Sex>male</Sex><Age>20</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>345769</Ticket><Fare>9.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>443</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Petterson, Mr. Johan Emil</Name><Sex>male</Sex><Age>25</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>347076</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>444</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Reynaldo, Ms. Encarnacion</Name><Sex>female</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>230434</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>445</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Johannesen-Bratthammer, Mr. Bernt</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>65306</Ticket><Fare>8.1125</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>446</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Dodge, Master. Washington</Name><Sex>male</Sex><Age>4</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>33638</Ticket><Fare>81.8583</Fare><Cabin>A34</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>447</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Mellinger, Miss. Madeleine Violet</Name><Sex>female</Sex><Age>13</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>250644</Ticket><Fare>19.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>448</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Seward, Mr. Frederic Kimber</Name><Sex>male</Sex><Age>34</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113794</Ticket><Fare>26.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>449</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Baclini, Miss. Marie Catherine</Name><Sex>female</Sex><Age>5</Age><SibSp>2</SibSp><Parch>1</Parch><Ticket>2666</Ticket><Fare>19.2583</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>450</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Peuchen, Major. Arthur Godfrey</Name><Sex>male</Sex><Age>52</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113786</Ticket><Fare>30.5</Fare><Cabin>C104</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>451</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>West, Mr. Edwy Arthur</Name><Sex>male</Sex><Age>36</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>C.A. 34651</Ticket><Fare>27.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>452</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Hagland, Mr. Ingvald Olai Olsen</Name><Sex>male</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>65303</Ticket><Fare>19.9667</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>453</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Foreman, Mr. Benjamin Laventall</Name><Sex>male</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113051</Ticket><Fare>27.75</Fare><Cabin>C111</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>454</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Goldenberg, Mr. Samuel L</Name><Sex>male</Sex><Age>49</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>17453</Ticket><Fare>89.1042</Fare><Cabin>C92</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>455</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Peduzzi, Mr. Joseph</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/5 2817</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>456</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Jalsevac, Mr. Ivan</Name><Sex>male</Sex><Age>29</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349240</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>457</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Millet, Mr. Francis Davis</Name><Sex>male</Sex><Age>65</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>13509</Ticket><Fare>26.55</Fare><Cabin>E38</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>458</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Kenyon, Mrs. Frederick R (Marion)</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>17464</Ticket><Fare>51.8625</Fare><Cabin>D21</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>459</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Toomey, Miss. Ellen</Name><Sex>female</Sex><Age>50</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>F.C.C. 13531</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>460</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>O'Connor, Mr. Maurice</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>371060</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>461</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Anderson, Mr. Harry</Name><Sex>male</Sex><Age>48</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>19952</Ticket><Fare>26.55</Fare><Cabin>E12</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>462</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Morley, Mr. William</Name><Sex>male</Sex><Age>34</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>364506</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>463</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Gee, Mr. Arthur H</Name><Sex>male</Sex><Age>47</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>111320</Ticket><Fare>38.5</Fare><Cabin>E63</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>464</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Milling, Mr. Jacob Christian</Name><Sex>male</Sex><Age>48</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>234360</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>465</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Maisner, Mr. Simon</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/S 2816</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>466</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Goncalves, Mr. Manuel Estanslas</Name><Sex>male</Sex><Age>38</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/O.Q. 3101306</Ticket><Fare>7.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>467</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Campbell, Mr. William</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>239853</Ticket><Fare>0</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>468</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Smart, Mr. John Montgomery</Name><Sex>male</Sex><Age>56</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113792</Ticket><Fare>26.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>469</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Scanlan, Mr. James</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>36209</Ticket><Fare>7.725</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>470</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Baclini, Miss. Helene Barbara</Name><Sex>female</Sex><Age>0.75</Age><SibSp>2</SibSp><Parch>1</Parch><Ticket>2666</Ticket><Fare>19.2583</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>471</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Keefe, Mr. Arthur</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>323592</Ticket><Fare>7.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>472</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Cacic, Mr. Luka</Name><Sex>male</Sex><Age>38</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>315089</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>473</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>West, Mrs. Edwy Arthur (Ada Mary Worth)</Name><Sex>female</Sex><Age>33</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>C.A. 34651</Ticket><Fare>27.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>474</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Jerwan, Mrs. Amin S (Marie Marthe Thuillard)</Name><Sex>female</Sex><Age>23</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SC/AH Basle 541</Ticket><Fare>13.7917</Fare><Cabin>D</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>475</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Strandberg, Miss. Ida Sofia</Name><Sex>female</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>7553</Ticket><Fare>9.8375</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>476</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Clifford, Mr. George Quincy</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>110465</Ticket><Fare>52</Fare><Cabin>A14</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>477</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Renouf, Mr. Peter Henry</Name><Sex>male</Sex><Age>34</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>31027</Ticket><Fare>21</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>478</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Braund, Mr. Lewis Richard</Name><Sex>male</Sex><Age>29</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>3460</Ticket><Fare>7.0458</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>479</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Karlsson, Mr. Nils August</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350060</Ticket><Fare>7.5208</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>480</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Hirvonen, Miss. Hildur E</Name><Sex>female</Sex><Age>2</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>3101298</Ticket><Fare>12.2875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>481</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Goodwin, Master. Harold Victor</Name><Sex>male</Sex><Age>9</Age><SibSp>5</SibSp><Parch>2</Parch><Ticket>CA 2144</Ticket><Fare>46.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>482</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Frost, Mr. Anthony Wood "Archie"</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>239854</Ticket><Fare>0</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>483</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rouse, Mr. Richard Henry</Name><Sex>male</Sex><Age>50</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/5 3594</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>484</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Turkula, Mrs. (Hedwig)</Name><Sex>female</Sex><Age>63</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>4134</Ticket><Fare>9.5875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>485</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Bishop, Mr. Dickinson H</Name><Sex>male</Sex><Age>25</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>11967</Ticket><Fare>91.0792</Fare><Cabin>B49</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>486</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lefebre, Miss. Jeannie</Name><Sex>female</Sex><Age></Age><SibSp>3</SibSp><Parch>1</Parch><Ticket>4133</Ticket><Fare>25.4667</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>487</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Hoyt, Mrs. Frederick Maxfield (Jane Anne Forby)</Name><Sex>female</Sex><Age>35</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>19943</Ticket><Fare>90</Fare><Cabin>C93</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>488</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Kent, Mr. Edward Austin</Name><Sex>male</Sex><Age>58</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>11771</Ticket><Fare>29.7</Fare><Cabin>B37</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>489</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Somerton, Mr. Francis William</Name><Sex>male</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A.5. 18509</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>490</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Coutts, Master. Eden Leslie "Neville"</Name><Sex>male</Sex><Age>9</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>C.A. 37671</Ticket><Fare>15.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>491</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Hagland, Mr. Konrad Mathias Reiersen</Name><Sex>male</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>65304</Ticket><Fare>19.9667</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>492</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Windelov, Mr. Einar</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/OQ 3101317</Ticket><Fare>7.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>493</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Molson, Mr. Harry Markland</Name><Sex>male</Sex><Age>55</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113787</Ticket><Fare>30.5</Fare><Cabin>C30</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>494</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Artagaveytia, Mr. Ramon</Name><Sex>male</Sex><Age>71</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17609</Ticket><Fare>49.5042</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>495</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Stanley, Mr. Edward Roland</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/4 45380</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>496</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Yousseff, Mr. Gerious</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2627</Ticket><Fare>14.4583</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>497</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Eustis, Miss. Elizabeth Mussey</Name><Sex>female</Sex><Age>54</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>36947</Ticket><Fare>78.2667</Fare><Cabin>D20</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>498</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Shellard, Mr. Frederick William</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 6212</Ticket><Fare>15.1</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>499</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Allison, Mrs. Hudson J C (Bessie Waldo Daniels)</Name><Sex>female</Sex><Age>25</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>113781</Ticket><Fare>151.55</Fare><Cabin>C22 C26</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>500</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Svensson, Mr. Olof</Name><Sex>male</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350035</Ticket><Fare>7.7958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>501</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Calic, Mr. Petar</Name><Sex>male</Sex><Age>17</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>315086</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>502</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Canavan, Miss. Mary</Name><Sex>female</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>364846</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>503</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>O'Sullivan, Miss. Bridget Mary</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>330909</Ticket><Fare>7.6292</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>504</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Laitinen, Miss. Kristina Sofia</Name><Sex>female</Sex><Age>37</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>4135</Ticket><Fare>9.5875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>505</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Maioni, Miss. Roberta</Name><Sex>female</Sex><Age>16</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>110152</Ticket><Fare>86.5</Fare><Cabin>B79</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>506</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Penasco y Castellana, Mr. Victor de Satode</Name><Sex>male</Sex><Age>18</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17758</Ticket><Fare>108.9</Fare><Cabin>C65</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>507</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Quick, Mrs. Frederick Charles (Jane Richards)</Name><Sex>female</Sex><Age>33</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>26360</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>508</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Bradley, Mr. George ("George Arthur Brayton")</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>111427</Ticket><Fare>26.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>509</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Olsen, Mr. Henry Margido</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C 4001</Ticket><Fare>22.525</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>510</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Lang, Mr. Fang</Name><Sex>male</Sex><Age>26</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>1601</Ticket><Fare>56.4958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>511</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Daly, Mr. Eugene Patrick</Name><Sex>male</Sex><Age>29</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>382651</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>512</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Webber, Mr. James</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/OQ 3101316</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>513</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>McGough, Mr. James Robert</Name><Sex>male</Sex><Age>36</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17473</Ticket><Fare>26.2875</Fare><Cabin>E25</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>514</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Rothschild, Mrs. Martin (Elizabeth L. Barrett)</Name><Sex>female</Sex><Age>54</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17603</Ticket><Fare>59.4</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>515</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Coleff, Mr. Satio</Name><Sex>male</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349209</Ticket><Fare>7.4958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>516</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Walker, Mr. William Anderson</Name><Sex>male</Sex><Age>47</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>36967</Ticket><Fare>34.0208</Fare><Cabin>D46</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>517</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Lemore, Mrs. (Amelia Milley)</Name><Sex>female</Sex><Age>34</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 34260</Ticket><Fare>10.5</Fare><Cabin>F33</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>518</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ryan, Mr. Patrick</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>371110</Ticket><Fare>24.15</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>519</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Angle, Mrs. William A (Florence "Mary" Agnes Hughes)</Name><Sex>female</Sex><Age>36</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>226875</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>520</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Pavlovic, Mr. Stefo</Name><Sex>male</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349242</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>521</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Perreault, Miss. Anne</Name><Sex>female</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>12749</Ticket><Fare>93.5</Fare><Cabin>B73</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>522</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Vovk, Mr. Janko</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349252</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>523</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lahoud, Mr. Sarkis</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2624</Ticket><Fare>7.225</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>524</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Hippach, Mrs. Louis Albert (Ida Sophia Fischer)</Name><Sex>female</Sex><Age>44</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>111361</Ticket><Fare>57.9792</Fare><Cabin>B18</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>525</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Kassem, Mr. Fared</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2700</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>526</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Farrell, Mr. James</Name><Sex>male</Sex><Age>40.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>367232</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>527</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Ridsdale, Miss. Lucy</Name><Sex>female</Sex><Age>50</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>W./C. 14258</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>528</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Farthing, Mr. John</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17483</Ticket><Fare>221.7792</Fare><Cabin>C95</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>529</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Salonen, Mr. Johan Werner</Name><Sex>male</Sex><Age>39</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>3101296</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>530</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Hocking, Mr. Richard George</Name><Sex>male</Sex><Age>23</Age><SibSp>2</SibSp><Parch>1</Parch><Ticket>29104</Ticket><Fare>11.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>531</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Quick, Miss. Phyllis May</Name><Sex>female</Sex><Age>2</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>26360</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>532</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Toufik, Mr. Nakli</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2641</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>533</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Elias, Mr. Joseph Jr</Name><Sex>male</Sex><Age>17</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>2690</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>534</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Peter, Mrs. Catherine (Catherine Rizk)</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>2668</Ticket><Fare>22.3583</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>535</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Cacic, Miss. Marija</Name><Sex>female</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>315084</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>536</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Hart, Miss. Eva Miriam</Name><Sex>female</Sex><Age>7</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>F.C.C. 13529</Ticket><Fare>26.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>537</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Butt, Major. Archibald Willingham</Name><Sex>male</Sex><Age>45</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113050</Ticket><Fare>26.55</Fare><Cabin>B38</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>538</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>LeRoy, Miss. Bertha</Name><Sex>female</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17761</Ticket><Fare>106.425</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>539</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Risien, Mr. Samuel Beard</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>364498</Ticket><Fare>14.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>540</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Frolicher, Miss. Hedwig Margaritha</Name><Sex>female</Sex><Age>22</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>13568</Ticket><Fare>49.5</Fare><Cabin>B39</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>541</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Crosby, Miss. Harriet R</Name><Sex>female</Sex><Age>36</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>WE/P 5735</Ticket><Fare>71</Fare><Cabin>B22</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>542</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Andersson, Miss. Ingeborg Constanzia</Name><Sex>female</Sex><Age>9</Age><SibSp>4</SibSp><Parch>2</Parch><Ticket>347082</Ticket><Fare>31.275</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>543</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Andersson, Miss. Sigrid Elisabeth</Name><Sex>female</Sex><Age>11</Age><SibSp>4</SibSp><Parch>2</Parch><Ticket>347082</Ticket><Fare>31.275</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>544</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Beane, Mr. Edward</Name><Sex>male</Sex><Age>32</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>2908</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>545</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Douglas, Mr. Walter Donald</Name><Sex>male</Sex><Age>50</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17761</Ticket><Fare>106.425</Fare><Cabin>C86</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>546</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Nicholson, Mr. Arthur Ernest</Name><Sex>male</Sex><Age>64</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>693</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>547</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Beane, Mrs. Edward (Ethel Clarke)</Name><Sex>female</Sex><Age>19</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>2908</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>548</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Padro y Manent, Mr. Julian</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SC/PARIS 2146</Ticket><Fare>13.8625</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>549</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Goldsmith, Mr. Frank John</Name><Sex>male</Sex><Age>33</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>363291</Ticket><Fare>20.525</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>550</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Davies, Master. John Morgan Jr</Name><Sex>male</Sex><Age>8</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>C.A. 33112</Ticket><Fare>36.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>551</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Thayer, Mr. John Borland Jr</Name><Sex>male</Sex><Age>17</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>17421</Ticket><Fare>110.8833</Fare><Cabin>C70</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>552</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Sharp, Mr. Percival James R</Name><Sex>male</Sex><Age>27</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>244358</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>553</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>O'Brien, Mr. Timothy</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>330979</Ticket><Fare>7.8292</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>554</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Leeni, Mr. Fahim ("Philip Zenni")</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2620</Ticket><Fare>7.225</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>555</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Ohman, Miss. Velin</Name><Sex>female</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347085</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>556</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Wright, Mr. George</Name><Sex>male</Sex><Age>62</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113807</Ticket><Fare>26.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>557</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Duff Gordon, Lady. (Lucille Christiana Sutherland) ("Mrs Morgan")</Name><Sex>female</Sex><Age>48</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>11755</Ticket><Fare>39.6</Fare><Cabin>A16</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>558</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Robbins, Mr. Victor</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17757</Ticket><Fare>227.525</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>559</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Taussig, Mrs. Emil (Tillie Mandelbaum)</Name><Sex>female</Sex><Age>39</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>110413</Ticket><Fare>79.65</Fare><Cabin>E67</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>560</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>de Messemaeker, Mrs. Guillaume Joseph (Emma)</Name><Sex>female</Sex><Age>36</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>345572</Ticket><Fare>17.4</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>561</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Morrow, Mr. Thomas Rowan</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>372622</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>562</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sivic, Mr. Husein</Name><Sex>male</Sex><Age>40</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349251</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>563</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Norman, Mr. Robert Douglas</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>218629</Ticket><Fare>13.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>564</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Simmons, Mr. John</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/OQ 392082</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>565</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Meanwell, Miss. (Marion Ogden)</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/O.Q. 392087</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>566</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Davies, Mr. Alfred J</Name><Sex>male</Sex><Age>24</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>A/4 48871</Ticket><Fare>24.15</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>567</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Stoytcheff, Mr. Ilia</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349205</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>568</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Palsson, Mrs. Nils (Alma Cornelia Berglund)</Name><Sex>female</Sex><Age>29</Age><SibSp>0</SibSp><Parch>4</Parch><Ticket>349909</Ticket><Fare>21.075</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>569</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Doharr, Mr. Tannous</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2686</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>570</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Jonsson, Mr. Carl</Name><Sex>male</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350417</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>571</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Harris, Mr. George</Name><Sex>male</Sex><Age>62</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>S.W./PP 752</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>572</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Appleton, Mrs. Edward Dale (Charlotte Lamson)</Name><Sex>female</Sex><Age>53</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>11769</Ticket><Fare>51.4792</Fare><Cabin>C101</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>573</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Flynn, Mr. John Irwin ("Irving")</Name><Sex>male</Sex><Age>36</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17474</Ticket><Fare>26.3875</Fare><Cabin>E25</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>574</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Kelly, Miss. Mary</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>14312</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>575</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rush, Mr. Alfred George John</Name><Sex>male</Sex><Age>16</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/4. 20589</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>576</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Patchett, Mr. George</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>358585</Ticket><Fare>14.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>577</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Garside, Miss. Ethel</Name><Sex>female</Sex><Age>34</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>243880</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>578</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Silvey, Mrs. William Baird (Alice Munger)</Name><Sex>female</Sex><Age>39</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>13507</Ticket><Fare>55.9</Fare><Cabin>E44</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>579</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Caram, Mrs. Joseph (Maria Elias)</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>2689</Ticket><Fare>14.4583</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>580</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Jussila, Mr. Eiriik</Name><Sex>male</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O 2. 3101286</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>581</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Christy, Miss. Julie Rachel</Name><Sex>female</Sex><Age>25</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>237789</Ticket><Fare>30</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>582</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Thayer, Mrs. John Borland (Marian Longstreth Morris)</Name><Sex>female</Sex><Age>39</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>17421</Ticket><Fare>110.8833</Fare><Cabin>C68</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>583</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Downton, Mr. William James</Name><Sex>male</Sex><Age>54</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>28403</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>584</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Ross, Mr. John Hugo</Name><Sex>male</Sex><Age>36</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>13049</Ticket><Fare>40.125</Fare><Cabin>A10</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>585</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Paulner, Mr. Uscher</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>3411</Ticket><Fare>8.7125</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>586</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Taussig, Miss. Ruth</Name><Sex>female</Sex><Age>18</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>110413</Ticket><Fare>79.65</Fare><Cabin>E68</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>587</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Jarvis, Mr. John Denzil</Name><Sex>male</Sex><Age>47</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>237565</Ticket><Fare>15</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>588</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Frolicher-Stehli, Mr. Maxmillian</Name><Sex>male</Sex><Age>60</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>13567</Ticket><Fare>79.2</Fare><Cabin>B41</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>589</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Gilinski, Mr. Eliezer</Name><Sex>male</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>14973</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>590</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Murdlin, Mr. Joseph</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A./5. 3235</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>591</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rintamaki, Mr. Matti</Name><Sex>male</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O 2. 3101273</Ticket><Fare>7.125</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>592</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Stephenson, Mrs. Walter Bertram (Martha Eustis)</Name><Sex>female</Sex><Age>52</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>36947</Ticket><Fare>78.2667</Fare><Cabin>D20</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>593</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Elsbury, Mr. William James</Name><Sex>male</Sex><Age>47</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/5 3902</Ticket><Fare>7.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>594</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Bourke, Miss. Mary</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>364848</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>595</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Chapman, Mr. John Henry</Name><Sex>male</Sex><Age>37</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>SC/AH 29037</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>596</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Van Impe, Mr. Jean Baptiste</Name><Sex>male</Sex><Age>36</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>345773</Ticket><Fare>24.15</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>597</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Leitch, Miss. Jessie Wills</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>248727</Ticket><Fare>33</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>598</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Johnson, Mr. Alfred</Name><Sex>male</Sex><Age>49</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>LINE</Ticket><Fare>0</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>599</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Boulos, Mr. Hanna</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2664</Ticket><Fare>7.225</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>600</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Duff Gordon, Sir. Cosmo Edmund ("Mr Morgan")</Name><Sex>male</Sex><Age>49</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17485</Ticket><Fare>56.9292</Fare><Cabin>A20</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>601</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Jacobsohn, Mrs. Sidney Samuel (Amy Frances Christy)</Name><Sex>female</Sex><Age>24</Age><SibSp>2</SibSp><Parch>1</Parch><Ticket>243847</Ticket><Fare>27</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>602</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Slabenoff, Mr. Petco</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349214</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>603</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Harrington, Mr. Charles H</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113796</Ticket><Fare>42.4</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>604</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Torber, Mr. Ernst William</Name><Sex>male</Sex><Age>44</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>364511</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>605</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Homer, Mr. Harry ("Mr E Haven")</Name><Sex>male</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>111426</Ticket><Fare>26.55</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>606</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lindell, Mr. Edvard Bengtsson</Name><Sex>male</Sex><Age>36</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>349910</Ticket><Fare>15.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>607</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Karaic, Mr. Milan</Name><Sex>male</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349246</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>608</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Daniel, Mr. Robert Williams</Name><Sex>male</Sex><Age>27</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113804</Ticket><Fare>30.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>609</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Laroche, Mrs. Joseph (Juliette Marie Louise Lafargue)</Name><Sex>female</Sex><Age>22</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>SC/Paris 2123</Ticket><Fare>41.5792</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>610</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Shutes, Miss. Elizabeth W</Name><Sex>female</Sex><Age>40</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17582</Ticket><Fare>153.4625</Fare><Cabin>C125</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>611</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Andersson, Mrs. Anders Johan (Alfrida Konstantia Brogren)</Name><Sex>female</Sex><Age>39</Age><SibSp>1</SibSp><Parch>5</Parch><Ticket>347082</Ticket><Fare>31.275</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>612</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Jardin, Mr. Jose Neto</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/O.Q. 3101305</Ticket><Fare>7.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>613</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Murphy, Miss. Margaret Jane</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>367230</Ticket><Fare>15.5</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>614</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Horgan, Mr. John</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>370377</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>615</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Brocklebank, Mr. William Alfred</Name><Sex>male</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>364512</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>616</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Herman, Miss. Alice</Name><Sex>female</Sex><Age>24</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>220845</Ticket><Fare>65</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>617</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Danbom, Mr. Ernst Gilbert</Name><Sex>male</Sex><Age>34</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>347080</Ticket><Fare>14.4</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>618</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lobb, Mrs. William Arthur (Cordelia K Stanlick)</Name><Sex>female</Sex><Age>26</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>A/5. 3336</Ticket><Fare>16.1</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>619</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Becker, Miss. Marion Louise</Name><Sex>female</Sex><Age>4</Age><SibSp>2</SibSp><Parch>1</Parch><Ticket>230136</Ticket><Fare>39</Fare><Cabin>F4</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>620</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Gavey, Mr. Lawrence</Name><Sex>male</Sex><Age>26</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>31028</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>621</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Yasbeck, Mr. Antoni</Name><Sex>male</Sex><Age>27</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>2659</Ticket><Fare>14.4542</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>622</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Kimball, Mr. Edwin Nelson Jr</Name><Sex>male</Sex><Age>42</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>11753</Ticket><Fare>52.5542</Fare><Cabin>D19</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>623</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Nakid, Mr. Sahid</Name><Sex>male</Sex><Age>20</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>2653</Ticket><Fare>15.7417</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>624</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Hansen, Mr. Henry Damsgaard</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350029</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>625</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Bowen, Mr. David John "Dai"</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>54636</Ticket><Fare>16.1</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>626</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Sutton, Mr. Frederick</Name><Sex>male</Sex><Age>61</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>36963</Ticket><Fare>32.3208</Fare><Cabin>D50</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>627</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Kirkland, Rev. Charles Leonard</Name><Sex>male</Sex><Age>57</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>219533</Ticket><Fare>12.35</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>628</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Longley, Miss. Gretchen Fiske</Name><Sex>female</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>13502</Ticket><Fare>77.9583</Fare><Cabin>D9</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>629</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Bostandyeff, Mr. Guentcho</Name><Sex>male</Sex><Age>26</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349224</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>630</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>O'Connell, Mr. Patrick D</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>334912</Ticket><Fare>7.7333</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>631</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Barkworth, Mr. Algernon Henry Wilson</Name><Sex>male</Sex><Age>80</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>27042</Ticket><Fare>30</Fare><Cabin>A23</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>632</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lundahl, Mr. Johan Svensson</Name><Sex>male</Sex><Age>51</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347743</Ticket><Fare>7.0542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>633</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Stahelin-Maeglin, Dr. Max</Name><Sex>male</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>13214</Ticket><Fare>30.5</Fare><Cabin>B50</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>634</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Parr, Mr. William Henry Marsh</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>112052</Ticket><Fare>0</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>635</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Skoog, Miss. Mabel</Name><Sex>female</Sex><Age>9</Age><SibSp>3</SibSp><Parch>2</Parch><Ticket>347088</Ticket><Fare>27.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>636</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Davis, Miss. Mary</Name><Sex>female</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>237668</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>637</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Leinonen, Mr. Antti Gustaf</Name><Sex>male</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O 2. 3101292</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>638</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Collyer, Mr. Harvey</Name><Sex>male</Sex><Age>31</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>C.A. 31921</Ticket><Fare>26.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>639</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Panula, Mrs. Juha (Maria Emilia Ojala)</Name><Sex>female</Sex><Age>41</Age><SibSp>0</SibSp><Parch>5</Parch><Ticket>3101295</Ticket><Fare>39.6875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>640</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Thorneycroft, Mr. Percival</Name><Sex>male</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>376564</Ticket><Fare>16.1</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>641</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Jensen, Mr. Hans Peder</Name><Sex>male</Sex><Age>20</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350050</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>642</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Sagesser, Mlle. Emma</Name><Sex>female</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17477</Ticket><Fare>69.3</Fare><Cabin>B35</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>643</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Skoog, Miss. Margit Elizabeth</Name><Sex>female</Sex><Age>2</Age><SibSp>3</SibSp><Parch>2</Parch><Ticket>347088</Ticket><Fare>27.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>644</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Foo, Mr. Choong</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>1601</Ticket><Fare>56.4958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>645</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Baclini, Miss. Eugenie</Name><Sex>female</Sex><Age>0.75</Age><SibSp>2</SibSp><Parch>1</Parch><Ticket>2666</Ticket><Fare>19.2583</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>646</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Harper, Mr. Henry Sleeper</Name><Sex>male</Sex><Age>48</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17572</Ticket><Fare>76.7292</Fare><Cabin>D33</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>647</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Cor, Mr. Liudevit</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349231</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>648</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Simonius-Blumer, Col. Oberst Alfons</Name><Sex>male</Sex><Age>56</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>13213</Ticket><Fare>35.5</Fare><Cabin>A26</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>649</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Willey, Mr. Edward</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>S.O./P.P. 751</Ticket><Fare>7.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>650</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Stanley, Miss. Amy Zillah Elsie</Name><Sex>female</Sex><Age>23</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>CA. 2314</Ticket><Fare>7.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>651</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Mitkoff, Mr. Mito</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349221</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>652</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Doling, Miss. Elsie</Name><Sex>female</Sex><Age>18</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>231919</Ticket><Fare>23</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>653</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Kalvik, Mr. Johannes Halvorsen</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>8475</Ticket><Fare>8.4333</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>654</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>O'Leary, Miss. Hanora "Norah"</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>330919</Ticket><Fare>7.8292</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>655</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Hegarty, Miss. Hanora "Nora"</Name><Sex>female</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>365226</Ticket><Fare>6.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>656</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Hickman, Mr. Leonard Mark</Name><Sex>male</Sex><Age>24</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>S.O.C. 14879</Ticket><Fare>73.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>657</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Radeff, Mr. Alexander</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349223</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>658</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Bourke, Mrs. John (Catherine)</Name><Sex>female</Sex><Age>32</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>364849</Ticket><Fare>15.5</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>659</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Eitemiller, Mr. George Floyd</Name><Sex>male</Sex><Age>23</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>29751</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>660</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Newell, Mr. Arthur Webster</Name><Sex>male</Sex><Age>58</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>35273</Ticket><Fare>113.275</Fare><Cabin>D48</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>661</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Frauenthal, Dr. Henry William</Name><Sex>male</Sex><Age>50</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>PC 17611</Ticket><Fare>133.65</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>662</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Badt, Mr. Mohamed</Name><Sex>male</Sex><Age>40</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2623</Ticket><Fare>7.225</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>663</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Colley, Mr. Edward Pomeroy</Name><Sex>male</Sex><Age>47</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>5727</Ticket><Fare>25.5875</Fare><Cabin>E58</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>664</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Coleff, Mr. Peju</Name><Sex>male</Sex><Age>36</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349210</Ticket><Fare>7.4958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>665</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Lindqvist, Mr. Eino William</Name><Sex>male</Sex><Age>20</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>STON/O 2. 3101285</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>666</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Hickman, Mr. Lewis</Name><Sex>male</Sex><Age>32</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>S.O.C. 14879</Ticket><Fare>73.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>667</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Butler, Mr. Reginald Fenton</Name><Sex>male</Sex><Age>25</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>234686</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>668</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rommetvedt, Mr. Knud Paust</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>312993</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>669</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Cook, Mr. Jacob</Name><Sex>male</Sex><Age>43</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/5 3536</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>670</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Taylor, Mrs. Elmer Zebley (Juliet Cummins Wright)</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>19996</Ticket><Fare>52</Fare><Cabin>C126</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>671</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Brown, Mrs. Thomas William Solomon (Elizabeth Catherine Ford)</Name><Sex>female</Sex><Age>40</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>29750</Ticket><Fare>39</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>672</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Davidson, Mr. Thornton</Name><Sex>male</Sex><Age>31</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>F.C. 12750</Ticket><Fare>52</Fare><Cabin>B71</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>673</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Mitchell, Mr. Henry Michael</Name><Sex>male</Sex><Age>70</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 24580</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>674</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Wilhelms, Mr. Charles</Name><Sex>male</Sex><Age>31</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>244270</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>675</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Watson, Mr. Ennis Hastings</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>239856</Ticket><Fare>0</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>676</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Edvardsson, Mr. Gustaf Hjalmar</Name><Sex>male</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349912</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>677</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sawyer, Mr. Frederick Charles</Name><Sex>male</Sex><Age>24.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>342826</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>678</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Turja, Miss. Anna Sofia</Name><Sex>female</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>4138</Ticket><Fare>9.8417</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>679</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Goodwin, Mrs. Frederick (Augusta Tyler)</Name><Sex>female</Sex><Age>43</Age><SibSp>1</SibSp><Parch>6</Parch><Ticket>CA 2144</Ticket><Fare>46.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>680</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Cardeza, Mr. Thomas Drake Martinez</Name><Sex>male</Sex><Age>36</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>PC 17755</Ticket><Fare>512.3292</Fare><Cabin>B51 B53 B55</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>681</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Peters, Miss. Katie</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>330935</Ticket><Fare>8.1375</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>682</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Hassab, Mr. Hammad</Name><Sex>male</Sex><Age>27</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17572</Ticket><Fare>76.7292</Fare><Cabin>D49</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>683</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Olsvigen, Mr. Thor Anderson</Name><Sex>male</Sex><Age>20</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>6563</Ticket><Fare>9.225</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>684</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Goodwin, Mr. Charles Edward</Name><Sex>male</Sex><Age>14</Age><SibSp>5</SibSp><Parch>2</Parch><Ticket>CA 2144</Ticket><Fare>46.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>685</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Brown, Mr. Thomas William Solomon</Name><Sex>male</Sex><Age>60</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>29750</Ticket><Fare>39</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>686</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Laroche, Mr. Joseph Philippe Lemercier</Name><Sex>male</Sex><Age>25</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>SC/Paris 2123</Ticket><Fare>41.5792</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>687</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Panula, Mr. Jaako Arnold</Name><Sex>male</Sex><Age>14</Age><SibSp>4</SibSp><Parch>1</Parch><Ticket>3101295</Ticket><Fare>39.6875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>688</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Dakic, Mr. Branko</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349228</Ticket><Fare>10.1708</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>689</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Fischer, Mr. Eberhard Thelander</Name><Sex>male</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350036</Ticket><Fare>7.7958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>690</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Madill, Miss. Georgette Alexandra</Name><Sex>female</Sex><Age>15</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>24160</Ticket><Fare>211.3375</Fare><Cabin>B5</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>691</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Dick, Mr. Albert Adrian</Name><Sex>male</Sex><Age>31</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>17474</Ticket><Fare>57</Fare><Cabin>B20</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>692</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Karun, Miss. Manca</Name><Sex>female</Sex><Age>4</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>349256</Ticket><Fare>13.4167</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>693</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Lam, Mr. Ali</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>1601</Ticket><Fare>56.4958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>694</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Saad, Mr. Khalil</Name><Sex>male</Sex><Age>25</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2672</Ticket><Fare>7.225</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>695</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Weir, Col. John</Name><Sex>male</Sex><Age>60</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113800</Ticket><Fare>26.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>696</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Chapman, Mr. Charles Henry</Name><Sex>male</Sex><Age>52</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>248731</Ticket><Fare>13.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>697</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Kelly, Mr. James</Name><Sex>male</Sex><Age>44</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>363592</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>698</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Mullens, Miss. Katherine "Katie"</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>35852</Ticket><Fare>7.7333</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>699</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Thayer, Mr. John Borland</Name><Sex>male</Sex><Age>49</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>17421</Ticket><Fare>110.8833</Fare><Cabin>C68</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>700</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Humblen, Mr. Adolf Mathias Nicolai Olsen</Name><Sex>male</Sex><Age>42</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>348121</Ticket><Fare>7.65</Fare><Cabin>F G63</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>701</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Astor, Mrs. John Jacob (Madeleine Talmadge Force)</Name><Sex>female</Sex><Age>18</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>PC 17757</Ticket><Fare>227.525</Fare><Cabin>C62 C64</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>702</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Silverthorne, Mr. Spencer Victor</Name><Sex>male</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17475</Ticket><Fare>26.2875</Fare><Cabin>E24</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>703</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Barbara, Miss. Saiide</Name><Sex>female</Sex><Age>18</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>2691</Ticket><Fare>14.4542</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>704</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Gallagher, Mr. Martin</Name><Sex>male</Sex><Age>25</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>36864</Ticket><Fare>7.7417</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>705</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Hansen, Mr. Henrik Juul</Name><Sex>male</Sex><Age>26</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>350025</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>706</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Morley, Mr. Henry Samuel ("Mr Henry Marshall")</Name><Sex>male</Sex><Age>39</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>250655</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>707</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Kelly, Mrs. Florence "Fannie"</Name><Sex>female</Sex><Age>45</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>223596</Ticket><Fare>13.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>708</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Calderhead, Mr. Edward Pennington</Name><Sex>male</Sex><Age>42</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17476</Ticket><Fare>26.2875</Fare><Cabin>E24</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>709</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Cleaver, Miss. Alice</Name><Sex>female</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113781</Ticket><Fare>151.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>710</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Moubarek, Master. Halim Gonios ("William George")</Name><Sex>male</Sex><Age></Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>2661</Ticket><Fare>15.2458</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>711</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Mayne, Mlle. Berthe Antonine ("Mrs de Villiers")</Name><Sex>female</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17482</Ticket><Fare>49.5042</Fare><Cabin>C90</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>712</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Klaber, Mr. Herman</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113028</Ticket><Fare>26.55</Fare><Cabin>C124</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>713</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Taylor, Mr. Elmer Zebley</Name><Sex>male</Sex><Age>48</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>19996</Ticket><Fare>52</Fare><Cabin>C126</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>714</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Larsson, Mr. August Viktor</Name><Sex>male</Sex><Age>29</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>7545</Ticket><Fare>9.4833</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>715</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Greenberg, Mr. Samuel</Name><Sex>male</Sex><Age>52</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>250647</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>716</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Soholt, Mr. Peter Andreas Lauritz Andersen</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>348124</Ticket><Fare>7.65</Fare><Cabin>F G73</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>717</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Endres, Miss. Caroline Louise</Name><Sex>female</Sex><Age>38</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17757</Ticket><Fare>227.525</Fare><Cabin>C45</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>718</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Troutt, Miss. Edwina Celia "Winnie"</Name><Sex>female</Sex><Age>27</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>34218</Ticket><Fare>10.5</Fare><Cabin>E101</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>719</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>McEvoy, Mr. Michael</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>36568</Ticket><Fare>15.5</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>720</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Johnson, Mr. Malkolm Joackim</Name><Sex>male</Sex><Age>33</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347062</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>721</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Harper, Miss. Annie Jessie "Nina"</Name><Sex>female</Sex><Age>6</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>248727</Ticket><Fare>33</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>722</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Jensen, Mr. Svend Lauritz</Name><Sex>male</Sex><Age>17</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>350048</Ticket><Fare>7.0542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>723</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Gillespie, Mr. William Henry</Name><Sex>male</Sex><Age>34</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>12233</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>724</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Hodges, Mr. Henry Price</Name><Sex>male</Sex><Age>50</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>250643</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>725</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Chambers, Mr. Norman Campbell</Name><Sex>male</Sex><Age>27</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>113806</Ticket><Fare>53.1</Fare><Cabin>E8</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>726</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Oreskovic, Mr. Luka</Name><Sex>male</Sex><Age>20</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>315094</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>727</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Renouf, Mrs. Peter Henry (Lillian Jefferys)</Name><Sex>female</Sex><Age>30</Age><SibSp>3</SibSp><Parch>0</Parch><Ticket>31027</Ticket><Fare>21</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>728</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Mannion, Miss. Margareth</Name><Sex>female</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>36866</Ticket><Fare>7.7375</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>729</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Bryhl, Mr. Kurt Arnold Gottfrid</Name><Sex>male</Sex><Age>25</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>236853</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>730</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ilmakangas, Miss. Pieta Sofia</Name><Sex>female</Sex><Age>25</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>STON/O2. 3101271</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>731</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Allen, Miss. Elisabeth Walton</Name><Sex>female</Sex><Age>29</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>24160</Ticket><Fare>211.3375</Fare><Cabin>B5</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>732</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Hassan, Mr. Houssein G N</Name><Sex>male</Sex><Age>11</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2699</Ticket><Fare>18.7875</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>733</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Knight, Mr. Robert J</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>239855</Ticket><Fare>0</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>734</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Berriman, Mr. William John</Name><Sex>male</Sex><Age>23</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>28425</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>735</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Troupiansky, Mr. Moses Aaron</Name><Sex>male</Sex><Age>23</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>233639</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>736</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Williams, Mr. Leslie</Name><Sex>male</Sex><Age>28.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>54636</Ticket><Fare>16.1</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>737</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ford, Mrs. Edward (Margaret Ann Watson)</Name><Sex>female</Sex><Age>48</Age><SibSp>1</SibSp><Parch>3</Parch><Ticket>W./C. 6608</Ticket><Fare>34.375</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>738</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Lesurer, Mr. Gustave J</Name><Sex>male</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17755</Ticket><Fare>512.3292</Fare><Cabin>B101</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>739</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ivanoff, Mr. Kanio</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349201</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>740</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Nankoff, Mr. Minko</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349218</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>741</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Hawksford, Mr. Walter James</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>16988</Ticket><Fare>30</Fare><Cabin>D45</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>742</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Cavendish, Mr. Tyrell William</Name><Sex>male</Sex><Age>36</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>19877</Ticket><Fare>78.85</Fare><Cabin>C46</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>743</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Ryerson, Miss. Susan Parker "Suzette"</Name><Sex>female</Sex><Age>21</Age><SibSp>2</SibSp><Parch>2</Parch><Ticket>PC 17608</Ticket><Fare>262.375</Fare><Cabin>B57 B59 B63 B66</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>744</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>McNamee, Mr. Neal</Name><Sex>male</Sex><Age>24</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>376566</Ticket><Fare>16.1</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>745</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Stranden, Mr. Juho</Name><Sex>male</Sex><Age>31</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O 2. 3101288</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>746</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Crosby, Capt. Edward Gifford</Name><Sex>male</Sex><Age>70</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>WE/P 5735</Ticket><Fare>71</Fare><Cabin>B22</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>747</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Abbott, Mr. Rossmore Edward</Name><Sex>male</Sex><Age>16</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>C.A. 2673</Ticket><Fare>20.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>748</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Sinkkonen, Miss. Anna</Name><Sex>female</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>250648</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>749</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Marvin, Mr. Daniel Warner</Name><Sex>male</Sex><Age>19</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>113773</Ticket><Fare>53.1</Fare><Cabin>D30</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>750</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Connaghton, Mr. Michael</Name><Sex>male</Sex><Age>31</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>335097</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>751</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Wells, Miss. Joan</Name><Sex>female</Sex><Age>4</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>29103</Ticket><Fare>23</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>752</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Moor, Master. Meier</Name><Sex>male</Sex><Age>6</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>392096</Ticket><Fare>12.475</Fare><Cabin>E121</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>753</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Vande Velde, Mr. Johannes Joseph</Name><Sex>male</Sex><Age>33</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>345780</Ticket><Fare>9.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>754</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Jonkoff, Mr. Lalio</Name><Sex>male</Sex><Age>23</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349204</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>755</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Herman, Mrs. Samuel (Jane Laver)</Name><Sex>female</Sex><Age>48</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>220845</Ticket><Fare>65</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>756</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Hamalainen, Master. Viljo</Name><Sex>male</Sex><Age>0.67</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>250649</Ticket><Fare>14.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>757</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Carlsson, Mr. August Sigfrid</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350042</Ticket><Fare>7.7958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>758</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Bailey, Mr. Percy Andrew</Name><Sex>male</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>29108</Ticket><Fare>11.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>759</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Theobald, Mr. Thomas Leonard</Name><Sex>male</Sex><Age>34</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>363294</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>760</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Rothes, the Countess. of (Lucy Noel Martha Dyer-Edwards)</Name><Sex>female</Sex><Age>33</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>110152</Ticket><Fare>86.5</Fare><Cabin>B77</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>761</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Garfirth, Mr. John</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>358585</Ticket><Fare>14.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>762</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Nirva, Mr. Iisakki Antino Aijo</Name><Sex>male</Sex><Age>41</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/O2 3101272</Ticket><Fare>7.125</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>763</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Barah, Mr. Hanna Assi</Name><Sex>male</Sex><Age>20</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2663</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>764</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Carter, Mrs. William Ernest (Lucile Polk)</Name><Sex>female</Sex><Age>36</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>113760</Ticket><Fare>120</Fare><Cabin>B96 B98</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>765</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Eklund, Mr. Hans Linus</Name><Sex>male</Sex><Age>16</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347074</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>766</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Hogeboom, Mrs. John C (Anna Andrews)</Name><Sex>female</Sex><Age>51</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>13502</Ticket><Fare>77.9583</Fare><Cabin>D11</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>767</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Brewe, Dr. Arthur Jackson</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>112379</Ticket><Fare>39.6</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>768</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Mangan, Miss. Mary</Name><Sex>female</Sex><Age>30.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>364850</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>769</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Moran, Mr. Daniel J</Name><Sex>male</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>371110</Ticket><Fare>24.15</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>770</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Gronnestad, Mr. Daniel Danielsen</Name><Sex>male</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>8471</Ticket><Fare>8.3625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>771</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lievens, Mr. Rene Aime</Name><Sex>male</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>345781</Ticket><Fare>9.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>772</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Jensen, Mr. Niels Peder</Name><Sex>male</Sex><Age>48</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>350047</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>773</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Mack, Mrs. (Mary)</Name><Sex>female</Sex><Age>57</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>S.O./P.P. 3</Ticket><Fare>10.5</Fare><Cabin>E77</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>774</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Elias, Mr. Dibo</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2674</Ticket><Fare>7.225</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>775</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Hocking, Mrs. Elizabeth (Eliza Needs)</Name><Sex>female</Sex><Age>54</Age><SibSp>1</SibSp><Parch>3</Parch><Ticket>29105</Ticket><Fare>23</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>776</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Myhrman, Mr. Pehr Fabian Oliver Malkolm</Name><Sex>male</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347078</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>777</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Tobin, Mr. Roger</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>383121</Ticket><Fare>7.75</Fare><Cabin>F38</Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>778</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Emanuel, Miss. Virginia Ethel</Name><Sex>female</Sex><Age>5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>364516</Ticket><Fare>12.475</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>779</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Kilgannon, Mr. Thomas J</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>36865</Ticket><Fare>7.7375</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>780</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Robert, Mrs. Edward Scott (Elisabeth Walton McMillan)</Name><Sex>female</Sex><Age>43</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>24160</Ticket><Fare>211.3375</Fare><Cabin>B3</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>781</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Ayoub, Miss. Banoura</Name><Sex>female</Sex><Age>13</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2687</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>782</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Dick, Mrs. Albert Adrian (Vera Gillespie)</Name><Sex>female</Sex><Age>17</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>17474</Ticket><Fare>57</Fare><Cabin>B20</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>783</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Long, Mr. Milton Clyde</Name><Sex>male</Sex><Age>29</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113501</Ticket><Fare>30</Fare><Cabin>D6</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>784</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Johnston, Mr. Andrew G</Name><Sex>male</Sex><Age></Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>W./C. 6607</Ticket><Fare>23.45</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>785</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ali, Mr. William</Name><Sex>male</Sex><Age>25</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/O.Q. 3101312</Ticket><Fare>7.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>786</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Harmer, Mr. Abraham (David Lishin)</Name><Sex>male</Sex><Age>25</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>374887</Ticket><Fare>7.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>787</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Sjoblom, Miss. Anna Sofia</Name><Sex>female</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>3101265</Ticket><Fare>7.4958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>788</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rice, Master. George Hugh</Name><Sex>male</Sex><Age>8</Age><SibSp>4</SibSp><Parch>1</Parch><Ticket>382652</Ticket><Fare>29.125</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>789</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Dean, Master. Bertram Vere</Name><Sex>male</Sex><Age>1</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>C.A. 2315</Ticket><Fare>20.575</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>790</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Guggenheim, Mr. Benjamin</Name><Sex>male</Sex><Age>46</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17593</Ticket><Fare>79.2</Fare><Cabin>B82 B84</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>791</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Keane, Mr. Andrew "Andy"</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>12460</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>792</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Gaskell, Mr. Alfred</Name><Sex>male</Sex><Age>16</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>239865</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>793</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sage, Miss. Stella Anna</Name><Sex>female</Sex><Age></Age><SibSp>8</SibSp><Parch>2</Parch><Ticket>CA. 2343</Ticket><Fare>69.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>794</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Hoyt, Mr. William Fisher</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17600</Ticket><Fare>30.6958</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>795</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Dantcheff, Mr. Ristiu</Name><Sex>male</Sex><Age>25</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349203</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>796</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Otter, Mr. Richard</Name><Sex>male</Sex><Age>39</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>28213</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>797</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Leader, Dr. Alice (Farnham)</Name><Sex>female</Sex><Age>49</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>17465</Ticket><Fare>25.9292</Fare><Cabin>D17</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>798</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Osman, Mrs. Mara</Name><Sex>female</Sex><Age>31</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349244</Ticket><Fare>8.6833</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>799</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Ibrahim Shawah, Mr. Yousseff</Name><Sex>male</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2685</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>800</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Van Impe, Mrs. Jean Baptiste (Rosalie Paula Govaert)</Name><Sex>female</Sex><Age>30</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>345773</Ticket><Fare>24.15</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>801</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Ponesell, Mr. Martin</Name><Sex>male</Sex><Age>34</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>250647</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>802</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Collyer, Mrs. Harvey (Charlotte Annie Tate)</Name><Sex>female</Sex><Age>31</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>C.A. 31921</Ticket><Fare>26.25</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>803</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Carter, Master. William Thornton II</Name><Sex>male</Sex><Age>11</Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>113760</Ticket><Fare>120</Fare><Cabin>B96 B98</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>804</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Thomas, Master. Assad Alexander</Name><Sex>male</Sex><Age>0.42</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>2625</Ticket><Fare>8.5167</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>805</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Hedman, Mr. Oskar Arvid</Name><Sex>male</Sex><Age>27</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347089</Ticket><Fare>6.975</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>806</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Johansson, Mr. Karl Johan</Name><Sex>male</Sex><Age>31</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347063</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>807</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Andrews, Mr. Thomas Jr</Name><Sex>male</Sex><Age>39</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>112050</Ticket><Fare>0</Fare><Cabin>A36</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>808</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Pettersson, Miss. Ellen Natalia</Name><Sex>female</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347087</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>809</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Meyer, Mr. August</Name><Sex>male</Sex><Age>39</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>248723</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>810</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Chambers, Mrs. Norman Campbell (Bertha Griggs)</Name><Sex>female</Sex><Age>33</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>113806</Ticket><Fare>53.1</Fare><Cabin>E8</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>811</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Alexander, Mr. William</Name><Sex>male</Sex><Age>26</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>3474</Ticket><Fare>7.8875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>812</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lester, Mr. James</Name><Sex>male</Sex><Age>39</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>A/4 48871</Ticket><Fare>24.15</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>813</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Slemen, Mr. Richard James</Name><Sex>male</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>28206</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>814</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Andersson, Miss. Ebba Iris Alfrida</Name><Sex>female</Sex><Age>6</Age><SibSp>4</SibSp><Parch>2</Parch><Ticket>347082</Ticket><Fare>31.275</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>815</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Tomlin, Mr. Ernest Portage</Name><Sex>male</Sex><Age>30.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>364499</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>816</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Fry, Mr. Richard</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>112058</Ticket><Fare>0</Fare><Cabin>B102</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>817</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Heininen, Miss. Wendla Maria</Name><Sex>female</Sex><Age>23</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>STON/O2. 3101290</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>818</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Mallet, Mr. Albert</Name><Sex>male</Sex><Age>31</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>S.C./PARIS 2079</Ticket><Fare>37.0042</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>819</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Holm, Mr. John Fredrik Alexander</Name><Sex>male</Sex><Age>43</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C 7075</Ticket><Fare>6.45</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>820</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Skoog, Master. Karl Thorsten</Name><Sex>male</Sex><Age>10</Age><SibSp>3</SibSp><Parch>2</Parch><Ticket>347088</Ticket><Fare>27.9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>821</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Hays, Mrs. Charles Melville (Clara Jennings Gregg)</Name><Sex>female</Sex><Age>52</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>12749</Ticket><Fare>93.5</Fare><Cabin>B69</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>822</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Lulic, Mr. Nikola</Name><Sex>male</Sex><Age>27</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>315098</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>823</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Reuchlin, Jonkheer. John George</Name><Sex>male</Sex><Age>38</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>19972</Ticket><Fare>0</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>824</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Moor, Mrs. (Beila)</Name><Sex>female</Sex><Age>27</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>392096</Ticket><Fare>12.475</Fare><Cabin>E121</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>825</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Panula, Master. Urho Abraham</Name><Sex>male</Sex><Age>2</Age><SibSp>4</SibSp><Parch>1</Parch><Ticket>3101295</Ticket><Fare>39.6875</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>826</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Flynn, Mr. John</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>368323</Ticket><Fare>6.95</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>827</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lam, Mr. Len</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>1601</Ticket><Fare>56.4958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>828</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Mallet, Master. Andre</Name><Sex>male</Sex><Age>1</Age><SibSp>0</SibSp><Parch>2</Parch><Ticket>S.C./PARIS 2079</Ticket><Fare>37.0042</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>829</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>McCormack, Mr. Thomas Joseph</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>367228</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>830</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Stone, Mrs. George Nelson (Martha Evelyn)</Name><Sex>female</Sex><Age>62</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113572</Ticket><Fare>80</Fare><Cabin>B28</Cabin><Embarked></Embarked></row>
    <row><PassengerId>831</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Yasbeck, Mrs. Antoni (Selini Alexander)</Name><Sex>female</Sex><Age>15</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>2659</Ticket><Fare>14.4542</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>832</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Richards, Master. George Sibley</Name><Sex>male</Sex><Age>0.83</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>29106</Ticket><Fare>18.75</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>833</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Saad, Mr. Amin</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2671</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>834</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Augustsson, Mr. Albert</Name><Sex>male</Sex><Age>23</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347468</Ticket><Fare>7.8542</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>835</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Allum, Mr. Owen George</Name><Sex>male</Sex><Age>18</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2223</Ticket><Fare>8.3</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>836</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Compton, Miss. Sara Rebecca</Name><Sex>female</Sex><Age>39</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>PC 17756</Ticket><Fare>83.1583</Fare><Cabin>E49</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>837</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Pasic, Mr. Jakob</Name><Sex>male</Sex><Age>21</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>315097</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>838</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sirota, Mr. Maurice</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>392092</Ticket><Fare>8.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>839</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Chip, Mr. Chang</Name><Sex>male</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>1601</Ticket><Fare>56.4958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>840</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Marechal, Mr. Pierre</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>11774</Ticket><Fare>29.7</Fare><Cabin>C47</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>841</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Alhomaki, Mr. Ilmari Rudolf</Name><Sex>male</Sex><Age>20</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/O2 3101287</Ticket><Fare>7.925</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>842</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Mudd, Mr. Thomas Charles</Name><Sex>male</Sex><Age>16</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>S.O./P.P. 3</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>843</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Serepeca, Miss. Augusta</Name><Sex>female</Sex><Age>30</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113798</Ticket><Fare>31</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>844</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Lemberopolous, Mr. Peter L</Name><Sex>male</Sex><Age>34.5</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2683</Ticket><Fare>6.4375</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>845</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Culumovic, Mr. Jeso</Name><Sex>male</Sex><Age>17</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>315090</Ticket><Fare>8.6625</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>846</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Abbing, Mr. Anthony</Name><Sex>male</Sex><Age>42</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A. 5547</Ticket><Fare>7.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>847</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sage, Mr. Douglas Bullen</Name><Sex>male</Sex><Age></Age><SibSp>8</SibSp><Parch>2</Parch><Ticket>CA. 2343</Ticket><Fare>69.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>848</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Markoff, Mr. Marin</Name><Sex>male</Sex><Age>35</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349213</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>849</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Harper, Rev. John</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>248727</Ticket><Fare>33</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>850</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Goldenberg, Mrs. Samuel L (Edwiga Grabowska)</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>17453</Ticket><Fare>89.1042</Fare><Cabin>C92</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>851</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Andersson, Master. Sigvard Harald Elias</Name><Sex>male</Sex><Age>4</Age><SibSp>4</SibSp><Parch>2</Parch><Ticket>347082</Ticket><Fare>31.275</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>852</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Svensson, Mr. Johan</Name><Sex>male</Sex><Age>74</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>347060</Ticket><Fare>7.775</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>853</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Boulos, Miss. Nourelain</Name><Sex>female</Sex><Age>9</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>2678</Ticket><Fare>15.2458</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>854</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Lines, Miss. Mary Conover</Name><Sex>female</Sex><Age>16</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>PC 17592</Ticket><Fare>39.4</Fare><Cabin>D28</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>855</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Carter, Mrs. Ernest Courtenay (Lilian Hughes)</Name><Sex>female</Sex><Age>44</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>244252</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>856</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Aks, Mrs. Sam (Leah Rosen)</Name><Sex>female</Sex><Age>18</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>392091</Ticket><Fare>9.35</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>857</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Wick, Mrs. George Dennick (Mary Hitchcock)</Name><Sex>female</Sex><Age>45</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>36928</Ticket><Fare>164.8667</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>858</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Daly, Mr. Peter Denis</Name><Sex>male</Sex><Age>51</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>113055</Ticket><Fare>26.55</Fare><Cabin>E17</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>859</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Baclini, Mrs. Solomon (Latifa Qurban)</Name><Sex>female</Sex><Age>24</Age><SibSp>0</SibSp><Parch>3</Parch><Ticket>2666</Ticket><Fare>19.2583</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>860</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Razi, Mr. Raihed</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2629</Ticket><Fare>7.2292</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>861</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Hansen, Mr. Claus Peter</Name><Sex>male</Sex><Age>41</Age><SibSp>2</SibSp><Parch>0</Parch><Ticket>350026</Ticket><Fare>14.1083</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>862</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Giles, Mr. Frederick Edward</Name><Sex>male</Sex><Age>21</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>28134</Ticket><Fare>11.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>863</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Swift, Mrs. Frederick Joel (Margaret Welles Barron)</Name><Sex>female</Sex><Age>48</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>17466</Ticket><Fare>25.9292</Fare><Cabin>D17</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>864</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sage, Miss. Dorothy Edith "Dolly"</Name><Sex>female</Sex><Age></Age><SibSp>8</SibSp><Parch>2</Parch><Ticket>CA. 2343</Ticket><Fare>69.55</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>865</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Gill, Mr. John William</Name><Sex>male</Sex><Age>24</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>233866</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>866</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Bystrom, Mrs. (Karolina)</Name><Sex>female</Sex><Age>42</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>236852</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>867</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Duran y More, Miss. Asuncion</Name><Sex>female</Sex><Age>27</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>SC/PARIS 2149</Ticket><Fare>13.8583</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>868</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Roebling, Mr. Washington Augustus II</Name><Sex>male</Sex><Age>31</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>PC 17590</Ticket><Fare>50.4958</Fare><Cabin>A24</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>869</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>van Melkebeke, Mr. Philemon</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>345777</Ticket><Fare>9.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>870</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Johnson, Master. Harold Theodor</Name><Sex>male</Sex><Age>4</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>347742</Ticket><Fare>11.1333</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>871</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Balkic, Mr. Cerin</Name><Sex>male</Sex><Age>26</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349248</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>872</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Beckwith, Mrs. Richard Leonard (Sallie Monypeny)</Name><Sex>female</Sex><Age>47</Age><SibSp>1</SibSp><Parch>1</Parch><Ticket>11751</Ticket><Fare>52.5542</Fare><Cabin>D35</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>873</PassengerId><Survived>0</Survived><Pclass>1</Pclass><Name>Carlsson, Mr. Frans Olof</Name><Sex>male</Sex><Age>33</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>695</Ticket><Fare>5</Fare><Cabin>B51 B53 B55</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>874</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Vander Cruyssen, Mr. Victor</Name><Sex>male</Sex><Age>47</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>345765</Ticket><Fare>9</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>875</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Abelson, Mrs. Samuel (Hannah Wizosky)</Name><Sex>female</Sex><Age>28</Age><SibSp>1</SibSp><Parch>0</Parch><Ticket>P/PP 3381</Ticket><Fare>24</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>876</PassengerId><Survived>1</Survived><Pclass>3</Pclass><Name>Najib, Miss. Adele Kiamie "Jane"</Name><Sex>female</Sex><Age>15</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>2667</Ticket><Fare>7.225</Fare><Cabin></Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>877</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Gustafsson, Mr. Alfred Ossian</Name><Sex>male</Sex><Age>20</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>7534</Ticket><Fare>9.8458</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>878</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Petroff, Mr. Nedelio</Name><Sex>male</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349212</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>879</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Laleff, Mr. Kristo</Name><Sex>male</Sex><Age></Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349217</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>880</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Potter, Mrs. Thomas Jr (Lily Alexenia Wilson)</Name><Sex>female</Sex><Age>56</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>11767</Ticket><Fare>83.1583</Fare><Cabin>C50</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>881</PassengerId><Survived>1</Survived><Pclass>2</Pclass><Name>Shelley, Mrs. William (Imanita Parrish Hall)</Name><Sex>female</Sex><Age>25</Age><SibSp>0</SibSp><Parch>1</Parch><Ticket>230433</Ticket><Fare>26</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>882</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Markun, Mr. Johann</Name><Sex>male</Sex><Age>33</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>349257</Ticket><Fare>7.8958</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>883</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Dahlberg, Miss. Gerda Ulrika</Name><Sex>female</Sex><Age>22</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>7552</Ticket><Fare>10.5167</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>884</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Banfield, Mr. Frederick James</Name><Sex>male</Sex><Age>28</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>C.A./SOTON 34068</Ticket><Fare>10.5</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>885</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Sutehall, Mr. Henry Jr</Name><Sex>male</Sex><Age>25</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>SOTON/OQ 392076</Ticket><Fare>7.05</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>886</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Rice, Mrs. William (Margaret Norton)</Name><Sex>female</Sex><Age>39</Age><SibSp>0</SibSp><Parch>5</Parch><Ticket>382652</Ticket><Fare>29.125</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
    <row><PassengerId>887</PassengerId><Survived>0</Survived><Pclass>2</Pclass><Name>Montvila, Rev. Juozas</Name><Sex>male</Sex><Age>27</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>211536</Ticket><Fare>13</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>888</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Graham, Miss. Margaret Edith</Name><Sex>female</Sex><Age>19</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>112053</Ticket><Fare>30</Fare><Cabin>B42</Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>889</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Johnston, Miss. Catherine Helen "Carrie"</Name><Sex>female</Sex><Age></Age><SibSp>1</SibSp><Parch>2</Parch><Ticket>W./C. 6607</Ticket><Fare>23.45</Fare><Cabin></Cabin><Embarked>S</Embarked></row>
    <row><PassengerId>890</PassengerId><Survived>1</Survived><Pclass>1</Pclass><Name>Behr, Mr. Karl Howell</Name><Sex>male</Sex><Age>26</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>111369</Ticket><Fare>30</Fare><Cabin>C148</Cabin><Embarked>C</Embarked></row>
    <row><PassengerId>891</PassengerId><Survived>0</Survived><Pclass>3</Pclass><Name>Dooley, Mr. Patrick</Name><Sex>male</Sex><Age>32</Age><SibSp>0</SibSp><Parch>0</Parch><Ticket>370376</Ticket><Fare>7.75</Fare><Cabin></Cabin><Embarked>Q</Embarked></row>
</pre>

## UnstructuredCSVLoader 

`UnstructuredCSVLoader` can be used in both `single` and `elements` mode. If you use the loader in “elements” mode, the CSV file will be a single Unstructured Table element. If you use the loader in elements” mode, an HTML representation of the table will be available in the `text_as_html` key in the document metadata.

```python
from langchain_community.document_loaders.csv_loader import UnstructuredCSVLoader

# Generate UnstructuredCSVLoader instance with elements mode
loader = UnstructuredCSVLoader(file_path="./data/titanic.csv", mode="elements")

docs = loader.load()

print(docs[0].metadata["text_as_html"])
```

<pre class="custom"><table border="1" class="dataframe">
      <tbody>
        <tr>
          <td>1</td>
          <td>0</td>
          <td>3</td>
          <td>Braund, Mr. Owen Harris</td>
          <td>male</td>
          <td>22.00</td>
          <td>1</td>
          <td>0</td>
          <td>A/5 21171</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>2</td>
          <td>1</td>
          <td>1</td>
          <td>Cumings, Mrs. John Bradley (Florence Briggs Thayer)</td>
          <td>female</td>
          <td>38.00</td>
          <td>1</td>
          <td>0</td>
          <td>PC 17599</td>
          <td>71.2833</td>
          <td>C85</td>
          <td>C</td>
        </tr>
        <tr>
          <td>3</td>
          <td>1</td>
          <td>3</td>
          <td>Heikkinen, Miss. Laina</td>
          <td>female</td>
          <td>26.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O2. 3101282</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>4</td>
          <td>1</td>
          <td>1</td>
          <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
          <td>female</td>
          <td>35.00</td>
          <td>1</td>
          <td>0</td>
          <td>113803</td>
          <td>53.1000</td>
          <td>C123</td>
          <td>S</td>
        </tr>
        <tr>
          <td>5</td>
          <td>0</td>
          <td>3</td>
          <td>Allen, Mr. William Henry</td>
          <td>male</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>373450</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>6</td>
          <td>0</td>
          <td>3</td>
          <td>Moran, Mr. James</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>330877</td>
          <td>8.4583</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>7</td>
          <td>0</td>
          <td>1</td>
          <td>McCarthy, Mr. Timothy J</td>
          <td>male</td>
          <td>54.00</td>
          <td>0</td>
          <td>0</td>
          <td>17463</td>
          <td>51.8625</td>
          <td>E46</td>
          <td>S</td>
        </tr>
        <tr>
          <td>8</td>
          <td>0</td>
          <td>3</td>
          <td>Palsson, Master. Gosta Leonard</td>
          <td>male</td>
          <td>2.00</td>
          <td>3</td>
          <td>1</td>
          <td>349909</td>
          <td>21.0750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>9</td>
          <td>1</td>
          <td>3</td>
          <td>Johnson, Mrs. Oscar W (Elisabeth Vilhelmina Berg)</td>
          <td>female</td>
          <td>27.00</td>
          <td>0</td>
          <td>2</td>
          <td>347742</td>
          <td>11.1333</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>10</td>
          <td>1</td>
          <td>2</td>
          <td>Nasser, Mrs. Nicholas (Adele Achem)</td>
          <td>female</td>
          <td>14.00</td>
          <td>1</td>
          <td>0</td>
          <td>237736</td>
          <td>30.0708</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>11</td>
          <td>1</td>
          <td>3</td>
          <td>Sandstrom, Miss. Marguerite Rut</td>
          <td>female</td>
          <td>4.00</td>
          <td>1</td>
          <td>1</td>
          <td>PP 9549</td>
          <td>16.7000</td>
          <td>G6</td>
          <td>S</td>
        </tr>
        <tr>
          <td>12</td>
          <td>1</td>
          <td>1</td>
          <td>Bonnell, Miss. Elizabeth</td>
          <td>female</td>
          <td>58.00</td>
          <td>0</td>
          <td>0</td>
          <td>113783</td>
          <td>26.5500</td>
          <td>C103</td>
          <td>S</td>
        </tr>
        <tr>
          <td>13</td>
          <td>0</td>
          <td>3</td>
          <td>Saundercock, Mr. William Henry</td>
          <td>male</td>
          <td>20.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/5. 2151</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>14</td>
          <td>0</td>
          <td>3</td>
          <td>Andersson, Mr. Anders Johan</td>
          <td>male</td>
          <td>39.00</td>
          <td>1</td>
          <td>5</td>
          <td>347082</td>
          <td>31.2750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>15</td>
          <td>0</td>
          <td>3</td>
          <td>Vestrom, Miss. Hulda Amanda Adolfina</td>
          <td>female</td>
          <td>14.00</td>
          <td>0</td>
          <td>0</td>
          <td>350406</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>16</td>
          <td>1</td>
          <td>2</td>
          <td>Hewlett, Mrs. (Mary D Kingcome)</td>
          <td>female</td>
          <td>55.00</td>
          <td>0</td>
          <td>0</td>
          <td>248706</td>
          <td>16.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>17</td>
          <td>0</td>
          <td>3</td>
          <td>Rice, Master. Eugene</td>
          <td>male</td>
          <td>2.00</td>
          <td>4</td>
          <td>1</td>
          <td>382652</td>
          <td>29.1250</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>18</td>
          <td>1</td>
          <td>2</td>
          <td>Williams, Mr. Charles Eugene</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>244373</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>19</td>
          <td>0</td>
          <td>3</td>
          <td>Vander Planke, Mrs. Julius (Emelia Maria Vandemoortele)</td>
          <td>female</td>
          <td>31.00</td>
          <td>1</td>
          <td>0</td>
          <td>345763</td>
          <td>18.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>20</td>
          <td>1</td>
          <td>3</td>
          <td>Masselmani, Mrs. Fatima</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2649</td>
          <td>7.2250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>21</td>
          <td>0</td>
          <td>2</td>
          <td>Fynney, Mr. Joseph J</td>
          <td>male</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>239865</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>22</td>
          <td>1</td>
          <td>2</td>
          <td>Beesley, Mr. Lawrence</td>
          <td>male</td>
          <td>34.00</td>
          <td>0</td>
          <td>0</td>
          <td>248698</td>
          <td>13.0000</td>
          <td>D56</td>
          <td>S</td>
        </tr>
        <tr>
          <td>23</td>
          <td>1</td>
          <td>3</td>
          <td>McGowan, Miss. Anna "Annie"</td>
          <td>female</td>
          <td>15.00</td>
          <td>0</td>
          <td>0</td>
          <td>330923</td>
          <td>8.0292</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>24</td>
          <td>1</td>
          <td>1</td>
          <td>Sloper, Mr. William Thompson</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>113788</td>
          <td>35.5000</td>
          <td>A6</td>
          <td>S</td>
        </tr>
        <tr>
          <td>25</td>
          <td>0</td>
          <td>3</td>
          <td>Palsson, Miss. Torborg Danira</td>
          <td>female</td>
          <td>8.00</td>
          <td>3</td>
          <td>1</td>
          <td>349909</td>
          <td>21.0750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>26</td>
          <td>1</td>
          <td>3</td>
          <td>Asplund, Mrs. Carl Oscar (Selma Augusta Emilia Johansson)</td>
          <td>female</td>
          <td>38.00</td>
          <td>1</td>
          <td>5</td>
          <td>347077</td>
          <td>31.3875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>27</td>
          <td>0</td>
          <td>3</td>
          <td>Emir, Mr. Farred Chehab</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2631</td>
          <td>7.2250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>28</td>
          <td>0</td>
          <td>1</td>
          <td>Fortune, Mr. Charles Alexander</td>
          <td>male</td>
          <td>19.00</td>
          <td>3</td>
          <td>2</td>
          <td>19950</td>
          <td>263.0000</td>
          <td>C23 C25 C27</td>
          <td>S</td>
        </tr>
        <tr>
          <td>29</td>
          <td>1</td>
          <td>3</td>
          <td>O'Dwyer, Miss. Ellen "Nellie"</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>330959</td>
          <td>7.8792</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>30</td>
          <td>0</td>
          <td>3</td>
          <td>Todoroff, Mr. Lalio</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349216</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>31</td>
          <td>0</td>
          <td>1</td>
          <td>Uruchurtu, Don. Manuel E</td>
          <td>male</td>
          <td>40.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17601</td>
          <td>27.7208</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>32</td>
          <td>1</td>
          <td>1</td>
          <td>Spencer, Mrs. William Augustus (Marie Eugenie)</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>PC 17569</td>
          <td>146.5208</td>
          <td>B78</td>
          <td>C</td>
        </tr>
        <tr>
          <td>33</td>
          <td>1</td>
          <td>3</td>
          <td>Glynn, Miss. Mary Agatha</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>335677</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>34</td>
          <td>0</td>
          <td>2</td>
          <td>Wheadon, Mr. Edward H</td>
          <td>male</td>
          <td>66.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 24579</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>35</td>
          <td>0</td>
          <td>1</td>
          <td>Meyer, Mr. Edgar Joseph</td>
          <td>male</td>
          <td>28.00</td>
          <td>1</td>
          <td>0</td>
          <td>PC 17604</td>
          <td>82.1708</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>36</td>
          <td>0</td>
          <td>1</td>
          <td>Holverson, Mr. Alexander Oskar</td>
          <td>male</td>
          <td>42.00</td>
          <td>1</td>
          <td>0</td>
          <td>113789</td>
          <td>52.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>37</td>
          <td>1</td>
          <td>3</td>
          <td>Mamee, Mr. Hanna</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2677</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>38</td>
          <td>0</td>
          <td>3</td>
          <td>Cann, Mr. Ernest Charles</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>A./5. 2152</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>39</td>
          <td>0</td>
          <td>3</td>
          <td>Vander Planke, Miss. Augusta Maria</td>
          <td>female</td>
          <td>18.00</td>
          <td>2</td>
          <td>0</td>
          <td>345764</td>
          <td>18.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>40</td>
          <td>1</td>
          <td>3</td>
          <td>Nicola-Yarred, Miss. Jamila</td>
          <td>female</td>
          <td>14.00</td>
          <td>1</td>
          <td>0</td>
          <td>2651</td>
          <td>11.2417</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>41</td>
          <td>0</td>
          <td>3</td>
          <td>Ahlin, Mrs. Johan (Johanna Persdotter Larsson)</td>
          <td>female</td>
          <td>40.00</td>
          <td>1</td>
          <td>0</td>
          <td>7546</td>
          <td>9.4750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>42</td>
          <td>0</td>
          <td>2</td>
          <td>Turpin, Mrs. William John Robert (Dorothy Ann Wonnacott)</td>
          <td>female</td>
          <td>27.00</td>
          <td>1</td>
          <td>0</td>
          <td>11668</td>
          <td>21.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>43</td>
          <td>0</td>
          <td>3</td>
          <td>Kraeff, Mr. Theodor</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349253</td>
          <td>7.8958</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>44</td>
          <td>1</td>
          <td>2</td>
          <td>Laroche, Miss. Simonne Marie Anne Andree</td>
          <td>female</td>
          <td>3.00</td>
          <td>1</td>
          <td>2</td>
          <td>SC/Paris 2123</td>
          <td>41.5792</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>45</td>
          <td>1</td>
          <td>3</td>
          <td>Devaney, Miss. Margaret Delia</td>
          <td>female</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>330958</td>
          <td>7.8792</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>46</td>
          <td>0</td>
          <td>3</td>
          <td>Rogers, Mr. William John</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>S.C./A.4. 23567</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>47</td>
          <td>0</td>
          <td>3</td>
          <td>Lennon, Mr. Denis</td>
          <td>male</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>370371</td>
          <td>15.5000</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>48</td>
          <td>1</td>
          <td>3</td>
          <td>O'Driscoll, Miss. Bridget</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>14311</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>49</td>
          <td>0</td>
          <td>3</td>
          <td>Samaan, Mr. Youssef</td>
          <td>male</td>
          <td></td>
          <td>2</td>
          <td>0</td>
          <td>2662</td>
          <td>21.6792</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>50</td>
          <td>0</td>
          <td>3</td>
          <td>Arnold-Franchi, Mrs. Josef (Josefine Franchi)</td>
          <td>female</td>
          <td>18.00</td>
          <td>1</td>
          <td>0</td>
          <td>349237</td>
          <td>17.8000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>51</td>
          <td>0</td>
          <td>3</td>
          <td>Panula, Master. Juha Niilo</td>
          <td>male</td>
          <td>7.00</td>
          <td>4</td>
          <td>1</td>
          <td>3101295</td>
          <td>39.6875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>52</td>
          <td>0</td>
          <td>3</td>
          <td>Nosworthy, Mr. Richard Cater</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/4. 39886</td>
          <td>7.8000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>53</td>
          <td>1</td>
          <td>1</td>
          <td>Harper, Mrs. Henry Sleeper (Myna Haxtun)</td>
          <td>female</td>
          <td>49.00</td>
          <td>1</td>
          <td>0</td>
          <td>PC 17572</td>
          <td>76.7292</td>
          <td>D33</td>
          <td>C</td>
        </tr>
        <tr>
          <td>54</td>
          <td>1</td>
          <td>2</td>
          <td>Faunthorpe, Mrs. Lizzie (Elizabeth Anne Wilkinson)</td>
          <td>female</td>
          <td>29.00</td>
          <td>1</td>
          <td>0</td>
          <td>2926</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>55</td>
          <td>0</td>
          <td>1</td>
          <td>Ostby, Mr. Engelhart Cornelius</td>
          <td>male</td>
          <td>65.00</td>
          <td>0</td>
          <td>1</td>
          <td>113509</td>
          <td>61.9792</td>
          <td>B30</td>
          <td>C</td>
        </tr>
        <tr>
          <td>56</td>
          <td>1</td>
          <td>1</td>
          <td>Woolner, Mr. Hugh</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>19947</td>
          <td>35.5000</td>
          <td>C52</td>
          <td>S</td>
        </tr>
        <tr>
          <td>57</td>
          <td>1</td>
          <td>2</td>
          <td>Rugg, Miss. Emily</td>
          <td>female</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 31026</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>58</td>
          <td>0</td>
          <td>3</td>
          <td>Novel, Mr. Mansouer</td>
          <td>male</td>
          <td>28.50</td>
          <td>0</td>
          <td>0</td>
          <td>2697</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>59</td>
          <td>1</td>
          <td>2</td>
          <td>West, Miss. Constance Mirium</td>
          <td>female</td>
          <td>5.00</td>
          <td>1</td>
          <td>2</td>
          <td>C.A. 34651</td>
          <td>27.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>60</td>
          <td>0</td>
          <td>3</td>
          <td>Goodwin, Master. William Frederick</td>
          <td>male</td>
          <td>11.00</td>
          <td>5</td>
          <td>2</td>
          <td>CA 2144</td>
          <td>46.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>61</td>
          <td>0</td>
          <td>3</td>
          <td>Sirayanian, Mr. Orsen</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>2669</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>62</td>
          <td>1</td>
          <td>1</td>
          <td>Icard, Miss. Amelie</td>
          <td>female</td>
          <td>38.00</td>
          <td>0</td>
          <td>0</td>
          <td>113572</td>
          <td>80.0000</td>
          <td>B28</td>
          <td></td>
        </tr>
        <tr>
          <td>63</td>
          <td>0</td>
          <td>1</td>
          <td>Harris, Mr. Henry Birkhardt</td>
          <td>male</td>
          <td>45.00</td>
          <td>1</td>
          <td>0</td>
          <td>36973</td>
          <td>83.4750</td>
          <td>C83</td>
          <td>S</td>
        </tr>
        <tr>
          <td>64</td>
          <td>0</td>
          <td>3</td>
          <td>Skoog, Master. Harald</td>
          <td>male</td>
          <td>4.00</td>
          <td>3</td>
          <td>2</td>
          <td>347088</td>
          <td>27.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>65</td>
          <td>0</td>
          <td>1</td>
          <td>Stewart, Mr. Albert A</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>PC 17605</td>
          <td>27.7208</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>66</td>
          <td>1</td>
          <td>3</td>
          <td>Moubarek, Master. Gerios</td>
          <td>male</td>
          <td></td>
          <td>1</td>
          <td>1</td>
          <td>2661</td>
          <td>15.2458</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>67</td>
          <td>1</td>
          <td>2</td>
          <td>Nye, Mrs. (Elizabeth Ramell)</td>
          <td>female</td>
          <td>29.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 29395</td>
          <td>10.5000</td>
          <td>F33</td>
          <td>S</td>
        </tr>
        <tr>
          <td>68</td>
          <td>0</td>
          <td>3</td>
          <td>Crease, Mr. Ernest James</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>S.P. 3464</td>
          <td>8.1583</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>69</td>
          <td>1</td>
          <td>3</td>
          <td>Andersson, Miss. Erna Alexandra</td>
          <td>female</td>
          <td>17.00</td>
          <td>4</td>
          <td>2</td>
          <td>3101281</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>70</td>
          <td>0</td>
          <td>3</td>
          <td>Kink, Mr. Vincenz</td>
          <td>male</td>
          <td>26.00</td>
          <td>2</td>
          <td>0</td>
          <td>315151</td>
          <td>8.6625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>71</td>
          <td>0</td>
          <td>2</td>
          <td>Jenkin, Mr. Stephen Curnow</td>
          <td>male</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 33111</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>72</td>
          <td>0</td>
          <td>3</td>
          <td>Goodwin, Miss. Lillian Amy</td>
          <td>female</td>
          <td>16.00</td>
          <td>5</td>
          <td>2</td>
          <td>CA 2144</td>
          <td>46.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>73</td>
          <td>0</td>
          <td>2</td>
          <td>Hood, Mr. Ambrose Jr</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>S.O.C. 14879</td>
          <td>73.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>74</td>
          <td>0</td>
          <td>3</td>
          <td>Chronopoulos, Mr. Apostolos</td>
          <td>male</td>
          <td>26.00</td>
          <td>1</td>
          <td>0</td>
          <td>2680</td>
          <td>14.4542</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>75</td>
          <td>1</td>
          <td>3</td>
          <td>Bing, Mr. Lee</td>
          <td>male</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>1601</td>
          <td>56.4958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>76</td>
          <td>0</td>
          <td>3</td>
          <td>Moen, Mr. Sigurd Hansen</td>
          <td>male</td>
          <td>25.00</td>
          <td>0</td>
          <td>0</td>
          <td>348123</td>
          <td>7.6500</td>
          <td>F G73</td>
          <td>S</td>
        </tr>
        <tr>
          <td>77</td>
          <td>0</td>
          <td>3</td>
          <td>Staneff, Mr. Ivan</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349208</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>78</td>
          <td>0</td>
          <td>3</td>
          <td>Moutal, Mr. Rahamin Haim</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>374746</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>79</td>
          <td>1</td>
          <td>2</td>
          <td>Caldwell, Master. Alden Gates</td>
          <td>male</td>
          <td>0.83</td>
          <td>0</td>
          <td>2</td>
          <td>248738</td>
          <td>29.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>80</td>
          <td>1</td>
          <td>3</td>
          <td>Dowdell, Miss. Elizabeth</td>
          <td>female</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>364516</td>
          <td>12.4750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>81</td>
          <td>0</td>
          <td>3</td>
          <td>Waelens, Mr. Achille</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>345767</td>
          <td>9.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>82</td>
          <td>1</td>
          <td>3</td>
          <td>Sheerlinck, Mr. Jan Baptist</td>
          <td>male</td>
          <td>29.00</td>
          <td>0</td>
          <td>0</td>
          <td>345779</td>
          <td>9.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>83</td>
          <td>1</td>
          <td>3</td>
          <td>McDermott, Miss. Brigdet Delia</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>330932</td>
          <td>7.7875</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>84</td>
          <td>0</td>
          <td>1</td>
          <td>Carrau, Mr. Francisco M</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>113059</td>
          <td>47.1000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>85</td>
          <td>1</td>
          <td>2</td>
          <td>Ilett, Miss. Bertha</td>
          <td>female</td>
          <td>17.00</td>
          <td>0</td>
          <td>0</td>
          <td>SO/C 14885</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>86</td>
          <td>1</td>
          <td>3</td>
          <td>Backstrom, Mrs. Karl Alfred (Maria Mathilda Gustafsson)</td>
          <td>female</td>
          <td>33.00</td>
          <td>3</td>
          <td>0</td>
          <td>3101278</td>
          <td>15.8500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>87</td>
          <td>0</td>
          <td>3</td>
          <td>Ford, Mr. William Neal</td>
          <td>male</td>
          <td>16.00</td>
          <td>1</td>
          <td>3</td>
          <td>W./C. 6608</td>
          <td>34.3750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>88</td>
          <td>0</td>
          <td>3</td>
          <td>Slocovski, Mr. Selman Francis</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/OQ 392086</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>89</td>
          <td>1</td>
          <td>1</td>
          <td>Fortune, Miss. Mabel Helen</td>
          <td>female</td>
          <td>23.00</td>
          <td>3</td>
          <td>2</td>
          <td>19950</td>
          <td>263.0000</td>
          <td>C23 C25 C27</td>
          <td>S</td>
        </tr>
        <tr>
          <td>90</td>
          <td>0</td>
          <td>3</td>
          <td>Celotti, Mr. Francesco</td>
          <td>male</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>343275</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>91</td>
          <td>0</td>
          <td>3</td>
          <td>Christmann, Mr. Emil</td>
          <td>male</td>
          <td>29.00</td>
          <td>0</td>
          <td>0</td>
          <td>343276</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>92</td>
          <td>0</td>
          <td>3</td>
          <td>Andreasson, Mr. Paul Edvin</td>
          <td>male</td>
          <td>20.00</td>
          <td>0</td>
          <td>0</td>
          <td>347466</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>93</td>
          <td>0</td>
          <td>1</td>
          <td>Chaffee, Mr. Herbert Fuller</td>
          <td>male</td>
          <td>46.00</td>
          <td>1</td>
          <td>0</td>
          <td>W.E.P. 5734</td>
          <td>61.1750</td>
          <td>E31</td>
          <td>S</td>
        </tr>
        <tr>
          <td>94</td>
          <td>0</td>
          <td>3</td>
          <td>Dean, Mr. Bertram Frank</td>
          <td>male</td>
          <td>26.00</td>
          <td>1</td>
          <td>2</td>
          <td>C.A. 2315</td>
          <td>20.5750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>95</td>
          <td>0</td>
          <td>3</td>
          <td>Coxon, Mr. Daniel</td>
          <td>male</td>
          <td>59.00</td>
          <td>0</td>
          <td>0</td>
          <td>364500</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>96</td>
          <td>0</td>
          <td>3</td>
          <td>Shorney, Mr. Charles Joseph</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>374910</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>97</td>
          <td>0</td>
          <td>1</td>
          <td>Goldschmidt, Mr. George B</td>
          <td>male</td>
          <td>71.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17754</td>
          <td>34.6542</td>
          <td>A5</td>
          <td>C</td>
        </tr>
        <tr>
          <td>98</td>
          <td>1</td>
          <td>1</td>
          <td>Greenfield, Mr. William Bertram</td>
          <td>male</td>
          <td>23.00</td>
          <td>0</td>
          <td>1</td>
          <td>PC 17759</td>
          <td>63.3583</td>
          <td>D10 D12</td>
          <td>C</td>
        </tr>
        <tr>
          <td>99</td>
          <td>1</td>
          <td>2</td>
          <td>Doling, Mrs. John T (Ada Julia Bone)</td>
          <td>female</td>
          <td>34.00</td>
          <td>0</td>
          <td>1</td>
          <td>231919</td>
          <td>23.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>100</td>
          <td>0</td>
          <td>2</td>
          <td>Kantor, Mr. Sinai</td>
          <td>male</td>
          <td>34.00</td>
          <td>1</td>
          <td>0</td>
          <td>244367</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>101</td>
          <td>0</td>
          <td>3</td>
          <td>Petranec, Miss. Matilda</td>
          <td>female</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>349245</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>102</td>
          <td>0</td>
          <td>3</td>
          <td>Petroff, Mr. Pastcho ("Pentcho")</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349215</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>103</td>
          <td>0</td>
          <td>1</td>
          <td>White, Mr. Richard Frasar</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>1</td>
          <td>35281</td>
          <td>77.2875</td>
          <td>D26</td>
          <td>S</td>
        </tr>
        <tr>
          <td>104</td>
          <td>0</td>
          <td>3</td>
          <td>Johansson, Mr. Gustaf Joel</td>
          <td>male</td>
          <td>33.00</td>
          <td>0</td>
          <td>0</td>
          <td>7540</td>
          <td>8.6542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>105</td>
          <td>0</td>
          <td>3</td>
          <td>Gustafsson, Mr. Anders Vilhelm</td>
          <td>male</td>
          <td>37.00</td>
          <td>2</td>
          <td>0</td>
          <td>3101276</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>106</td>
          <td>0</td>
          <td>3</td>
          <td>Mionoff, Mr. Stoytcho</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>349207</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>107</td>
          <td>1</td>
          <td>3</td>
          <td>Salkjelsvik, Miss. Anna Kristine</td>
          <td>female</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>343120</td>
          <td>7.6500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>108</td>
          <td>1</td>
          <td>3</td>
          <td>Moss, Mr. Albert Johan</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>312991</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>109</td>
          <td>0</td>
          <td>3</td>
          <td>Rekic, Mr. Tido</td>
          <td>male</td>
          <td>38.00</td>
          <td>0</td>
          <td>0</td>
          <td>349249</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>110</td>
          <td>1</td>
          <td>3</td>
          <td>Moran, Miss. Bertha</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>371110</td>
          <td>24.1500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>111</td>
          <td>0</td>
          <td>1</td>
          <td>Porter, Mr. Walter Chamberlain</td>
          <td>male</td>
          <td>47.00</td>
          <td>0</td>
          <td>0</td>
          <td>110465</td>
          <td>52.0000</td>
          <td>C110</td>
          <td>S</td>
        </tr>
        <tr>
          <td>112</td>
          <td>0</td>
          <td>3</td>
          <td>Zabour, Miss. Hileni</td>
          <td>female</td>
          <td>14.50</td>
          <td>1</td>
          <td>0</td>
          <td>2665</td>
          <td>14.4542</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>113</td>
          <td>0</td>
          <td>3</td>
          <td>Barton, Mr. David John</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>324669</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>114</td>
          <td>0</td>
          <td>3</td>
          <td>Jussila, Miss. Katriina</td>
          <td>female</td>
          <td>20.00</td>
          <td>1</td>
          <td>0</td>
          <td>4136</td>
          <td>9.8250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>115</td>
          <td>0</td>
          <td>3</td>
          <td>Attalah, Miss. Malake</td>
          <td>female</td>
          <td>17.00</td>
          <td>0</td>
          <td>0</td>
          <td>2627</td>
          <td>14.4583</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>116</td>
          <td>0</td>
          <td>3</td>
          <td>Pekoniemi, Mr. Edvard</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O 2. 3101294</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>117</td>
          <td>0</td>
          <td>3</td>
          <td>Connors, Mr. Patrick</td>
          <td>male</td>
          <td>70.50</td>
          <td>0</td>
          <td>0</td>
          <td>370369</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>118</td>
          <td>0</td>
          <td>2</td>
          <td>Turpin, Mr. William John Robert</td>
          <td>male</td>
          <td>29.00</td>
          <td>1</td>
          <td>0</td>
          <td>11668</td>
          <td>21.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>119</td>
          <td>0</td>
          <td>1</td>
          <td>Baxter, Mr. Quigg Edmond</td>
          <td>male</td>
          <td>24.00</td>
          <td>0</td>
          <td>1</td>
          <td>PC 17558</td>
          <td>247.5208</td>
          <td>B58 B60</td>
          <td>C</td>
        </tr>
        <tr>
          <td>120</td>
          <td>0</td>
          <td>3</td>
          <td>Andersson, Miss. Ellis Anna Maria</td>
          <td>female</td>
          <td>2.00</td>
          <td>4</td>
          <td>2</td>
          <td>347082</td>
          <td>31.2750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>121</td>
          <td>0</td>
          <td>2</td>
          <td>Hickman, Mr. Stanley George</td>
          <td>male</td>
          <td>21.00</td>
          <td>2</td>
          <td>0</td>
          <td>S.O.C. 14879</td>
          <td>73.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>122</td>
          <td>0</td>
          <td>3</td>
          <td>Moore, Mr. Leonard Charles</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>A4. 54510</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>123</td>
          <td>0</td>
          <td>2</td>
          <td>Nasser, Mr. Nicholas</td>
          <td>male</td>
          <td>32.50</td>
          <td>1</td>
          <td>0</td>
          <td>237736</td>
          <td>30.0708</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>124</td>
          <td>1</td>
          <td>2</td>
          <td>Webber, Miss. Susan</td>
          <td>female</td>
          <td>32.50</td>
          <td>0</td>
          <td>0</td>
          <td>27267</td>
          <td>13.0000</td>
          <td>E101</td>
          <td>S</td>
        </tr>
        <tr>
          <td>125</td>
          <td>0</td>
          <td>1</td>
          <td>White, Mr. Percival Wayland</td>
          <td>male</td>
          <td>54.00</td>
          <td>0</td>
          <td>1</td>
          <td>35281</td>
          <td>77.2875</td>
          <td>D26</td>
          <td>S</td>
        </tr>
        <tr>
          <td>126</td>
          <td>1</td>
          <td>3</td>
          <td>Nicola-Yarred, Master. Elias</td>
          <td>male</td>
          <td>12.00</td>
          <td>1</td>
          <td>0</td>
          <td>2651</td>
          <td>11.2417</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>127</td>
          <td>0</td>
          <td>3</td>
          <td>McMahon, Mr. Martin</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>370372</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>128</td>
          <td>1</td>
          <td>3</td>
          <td>Madsen, Mr. Fridtjof Arne</td>
          <td>male</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>C 17369</td>
          <td>7.1417</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>129</td>
          <td>1</td>
          <td>3</td>
          <td>Peter, Miss. Anna</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>1</td>
          <td>2668</td>
          <td>22.3583</td>
          <td>F E69</td>
          <td>C</td>
        </tr>
        <tr>
          <td>130</td>
          <td>0</td>
          <td>3</td>
          <td>Ekstrom, Mr. Johan</td>
          <td>male</td>
          <td>45.00</td>
          <td>0</td>
          <td>0</td>
          <td>347061</td>
          <td>6.9750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>131</td>
          <td>0</td>
          <td>3</td>
          <td>Drazenoic, Mr. Jozef</td>
          <td>male</td>
          <td>33.00</td>
          <td>0</td>
          <td>0</td>
          <td>349241</td>
          <td>7.8958</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>132</td>
          <td>0</td>
          <td>3</td>
          <td>Coelho, Mr. Domingos Fernandeo</td>
          <td>male</td>
          <td>20.00</td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/O.Q. 3101307</td>
          <td>7.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>133</td>
          <td>0</td>
          <td>3</td>
          <td>Robins, Mrs. Alexander A (Grace Charity Laury)</td>
          <td>female</td>
          <td>47.00</td>
          <td>1</td>
          <td>0</td>
          <td>A/5. 3337</td>
          <td>14.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>134</td>
          <td>1</td>
          <td>2</td>
          <td>Weisz, Mrs. Leopold (Mathilde Francoise Pede)</td>
          <td>female</td>
          <td>29.00</td>
          <td>1</td>
          <td>0</td>
          <td>228414</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>135</td>
          <td>0</td>
          <td>2</td>
          <td>Sobey, Mr. Samuel James Hayden</td>
          <td>male</td>
          <td>25.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 29178</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>136</td>
          <td>0</td>
          <td>2</td>
          <td>Richard, Mr. Emile</td>
          <td>male</td>
          <td>23.00</td>
          <td>0</td>
          <td>0</td>
          <td>SC/PARIS 2133</td>
          <td>15.0458</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>137</td>
          <td>1</td>
          <td>1</td>
          <td>Newsom, Miss. Helen Monypeny</td>
          <td>female</td>
          <td>19.00</td>
          <td>0</td>
          <td>2</td>
          <td>11752</td>
          <td>26.2833</td>
          <td>D47</td>
          <td>S</td>
        </tr>
        <tr>
          <td>138</td>
          <td>0</td>
          <td>1</td>
          <td>Futrelle, Mr. Jacques Heath</td>
          <td>male</td>
          <td>37.00</td>
          <td>1</td>
          <td>0</td>
          <td>113803</td>
          <td>53.1000</td>
          <td>C123</td>
          <td>S</td>
        </tr>
        <tr>
          <td>139</td>
          <td>0</td>
          <td>3</td>
          <td>Osen, Mr. Olaf Elon</td>
          <td>male</td>
          <td>16.00</td>
          <td>0</td>
          <td>0</td>
          <td>7534</td>
          <td>9.2167</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>140</td>
          <td>0</td>
          <td>1</td>
          <td>Giglio, Mr. Victor</td>
          <td>male</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17593</td>
          <td>79.2000</td>
          <td>B86</td>
          <td>C</td>
        </tr>
        <tr>
          <td>141</td>
          <td>0</td>
          <td>3</td>
          <td>Boulos, Mrs. Joseph (Sultana)</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>2</td>
          <td>2678</td>
          <td>15.2458</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>142</td>
          <td>1</td>
          <td>3</td>
          <td>Nysten, Miss. Anna Sofia</td>
          <td>female</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>347081</td>
          <td>7.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>143</td>
          <td>1</td>
          <td>3</td>
          <td>Hakkarainen, Mrs. Pekka Pietari (Elin Matilda Dolck)</td>
          <td>female</td>
          <td>24.00</td>
          <td>1</td>
          <td>0</td>
          <td>STON/O2. 3101279</td>
          <td>15.8500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>144</td>
          <td>0</td>
          <td>3</td>
          <td>Burke, Mr. Jeremiah</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>365222</td>
          <td>6.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>145</td>
          <td>0</td>
          <td>2</td>
          <td>Andrew, Mr. Edgardo Samuel</td>
          <td>male</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>231945</td>
          <td>11.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>146</td>
          <td>0</td>
          <td>2</td>
          <td>Nicholls, Mr. Joseph Charles</td>
          <td>male</td>
          <td>19.00</td>
          <td>1</td>
          <td>1</td>
          <td>C.A. 33112</td>
          <td>36.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>147</td>
          <td>1</td>
          <td>3</td>
          <td>Andersson, Mr. August Edvard ("Wennerstrom")</td>
          <td>male</td>
          <td>27.00</td>
          <td>0</td>
          <td>0</td>
          <td>350043</td>
          <td>7.7958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>148</td>
          <td>0</td>
          <td>3</td>
          <td>Ford, Miss. Robina Maggie "Ruby"</td>
          <td>female</td>
          <td>9.00</td>
          <td>2</td>
          <td>2</td>
          <td>W./C. 6608</td>
          <td>34.3750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>149</td>
          <td>0</td>
          <td>2</td>
          <td>Navratil, Mr. Michel ("Louis M Hoffman")</td>
          <td>male</td>
          <td>36.50</td>
          <td>0</td>
          <td>2</td>
          <td>230080</td>
          <td>26.0000</td>
          <td>F2</td>
          <td>S</td>
        </tr>
        <tr>
          <td>150</td>
          <td>0</td>
          <td>2</td>
          <td>Byles, Rev. Thomas Roussel Davids</td>
          <td>male</td>
          <td>42.00</td>
          <td>0</td>
          <td>0</td>
          <td>244310</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>151</td>
          <td>0</td>
          <td>2</td>
          <td>Bateman, Rev. Robert James</td>
          <td>male</td>
          <td>51.00</td>
          <td>0</td>
          <td>0</td>
          <td>S.O.P. 1166</td>
          <td>12.5250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>152</td>
          <td>1</td>
          <td>1</td>
          <td>Pears, Mrs. Thomas (Edith Wearne)</td>
          <td>female</td>
          <td>22.00</td>
          <td>1</td>
          <td>0</td>
          <td>113776</td>
          <td>66.6000</td>
          <td>C2</td>
          <td>S</td>
        </tr>
        <tr>
          <td>153</td>
          <td>0</td>
          <td>3</td>
          <td>Meo, Mr. Alfonzo</td>
          <td>male</td>
          <td>55.50</td>
          <td>0</td>
          <td>0</td>
          <td>A.5. 11206</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>154</td>
          <td>0</td>
          <td>3</td>
          <td>van Billiard, Mr. Austin Blyler</td>
          <td>male</td>
          <td>40.50</td>
          <td>0</td>
          <td>2</td>
          <td>A/5. 851</td>
          <td>14.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>155</td>
          <td>0</td>
          <td>3</td>
          <td>Olsen, Mr. Ole Martin</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>Fa 265302</td>
          <td>7.3125</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>156</td>
          <td>0</td>
          <td>1</td>
          <td>Williams, Mr. Charles Duane</td>
          <td>male</td>
          <td>51.00</td>
          <td>0</td>
          <td>1</td>
          <td>PC 17597</td>
          <td>61.3792</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>157</td>
          <td>1</td>
          <td>3</td>
          <td>Gilnagh, Miss. Katherine "Katie"</td>
          <td>female</td>
          <td>16.00</td>
          <td>0</td>
          <td>0</td>
          <td>35851</td>
          <td>7.7333</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>158</td>
          <td>0</td>
          <td>3</td>
          <td>Corn, Mr. Harry</td>
          <td>male</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/OQ 392090</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>159</td>
          <td>0</td>
          <td>3</td>
          <td>Smiljanic, Mr. Mile</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>315037</td>
          <td>8.6625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>160</td>
          <td>0</td>
          <td>3</td>
          <td>Sage, Master. Thomas Henry</td>
          <td>male</td>
          <td></td>
          <td>8</td>
          <td>2</td>
          <td>CA. 2343</td>
          <td>69.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>161</td>
          <td>0</td>
          <td>3</td>
          <td>Cribb, Mr. John Hatfield</td>
          <td>male</td>
          <td>44.00</td>
          <td>0</td>
          <td>1</td>
          <td>371362</td>
          <td>16.1000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>162</td>
          <td>1</td>
          <td>2</td>
          <td>Watt, Mrs. James (Elizabeth "Bessie" Inglis Milne)</td>
          <td>female</td>
          <td>40.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 33595</td>
          <td>15.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>163</td>
          <td>0</td>
          <td>3</td>
          <td>Bengtsson, Mr. John Viktor</td>
          <td>male</td>
          <td>26.00</td>
          <td>0</td>
          <td>0</td>
          <td>347068</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>164</td>
          <td>0</td>
          <td>3</td>
          <td>Calic, Mr. Jovo</td>
          <td>male</td>
          <td>17.00</td>
          <td>0</td>
          <td>0</td>
          <td>315093</td>
          <td>8.6625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>165</td>
          <td>0</td>
          <td>3</td>
          <td>Panula, Master. Eino Viljami</td>
          <td>male</td>
          <td>1.00</td>
          <td>4</td>
          <td>1</td>
          <td>3101295</td>
          <td>39.6875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>166</td>
          <td>1</td>
          <td>3</td>
          <td>Goldsmith, Master. Frank John William "Frankie"</td>
          <td>male</td>
          <td>9.00</td>
          <td>0</td>
          <td>2</td>
          <td>363291</td>
          <td>20.5250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>167</td>
          <td>1</td>
          <td>1</td>
          <td>Chibnall, Mrs. (Edith Martha Bowerman)</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>1</td>
          <td>113505</td>
          <td>55.0000</td>
          <td>E33</td>
          <td>S</td>
        </tr>
        <tr>
          <td>168</td>
          <td>0</td>
          <td>3</td>
          <td>Skoog, Mrs. William (Anna Bernhardina Karlsson)</td>
          <td>female</td>
          <td>45.00</td>
          <td>1</td>
          <td>4</td>
          <td>347088</td>
          <td>27.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>169</td>
          <td>0</td>
          <td>1</td>
          <td>Baumann, Mr. John D</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>PC 17318</td>
          <td>25.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>170</td>
          <td>0</td>
          <td>3</td>
          <td>Ling, Mr. Lee</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>1601</td>
          <td>56.4958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>171</td>
          <td>0</td>
          <td>1</td>
          <td>Van der hoef, Mr. Wyckoff</td>
          <td>male</td>
          <td>61.00</td>
          <td>0</td>
          <td>0</td>
          <td>111240</td>
          <td>33.5000</td>
          <td>B19</td>
          <td>S</td>
        </tr>
        <tr>
          <td>172</td>
          <td>0</td>
          <td>3</td>
          <td>Rice, Master. Arthur</td>
          <td>male</td>
          <td>4.00</td>
          <td>4</td>
          <td>1</td>
          <td>382652</td>
          <td>29.1250</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>173</td>
          <td>1</td>
          <td>3</td>
          <td>Johnson, Miss. Eleanor Ileen</td>
          <td>female</td>
          <td>1.00</td>
          <td>1</td>
          <td>1</td>
          <td>347742</td>
          <td>11.1333</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>174</td>
          <td>0</td>
          <td>3</td>
          <td>Sivola, Mr. Antti Wilhelm</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O 2. 3101280</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>175</td>
          <td>0</td>
          <td>1</td>
          <td>Smith, Mr. James Clinch</td>
          <td>male</td>
          <td>56.00</td>
          <td>0</td>
          <td>0</td>
          <td>17764</td>
          <td>30.6958</td>
          <td>A7</td>
          <td>C</td>
        </tr>
        <tr>
          <td>176</td>
          <td>0</td>
          <td>3</td>
          <td>Klasen, Mr. Klas Albin</td>
          <td>male</td>
          <td>18.00</td>
          <td>1</td>
          <td>1</td>
          <td>350404</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>177</td>
          <td>0</td>
          <td>3</td>
          <td>Lefebre, Master. Henry Forbes</td>
          <td>male</td>
          <td></td>
          <td>3</td>
          <td>1</td>
          <td>4133</td>
          <td>25.4667</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>178</td>
          <td>0</td>
          <td>1</td>
          <td>Isham, Miss. Ann Elizabeth</td>
          <td>female</td>
          <td>50.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17595</td>
          <td>28.7125</td>
          <td>C49</td>
          <td>C</td>
        </tr>
        <tr>
          <td>179</td>
          <td>0</td>
          <td>2</td>
          <td>Hale, Mr. Reginald</td>
          <td>male</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>250653</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>180</td>
          <td>0</td>
          <td>3</td>
          <td>Leonard, Mr. Lionel</td>
          <td>male</td>
          <td>36.00</td>
          <td>0</td>
          <td>0</td>
          <td>LINE</td>
          <td>0.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>181</td>
          <td>0</td>
          <td>3</td>
          <td>Sage, Miss. Constance Gladys</td>
          <td>female</td>
          <td></td>
          <td>8</td>
          <td>2</td>
          <td>CA. 2343</td>
          <td>69.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>182</td>
          <td>0</td>
          <td>2</td>
          <td>Pernot, Mr. Rene</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>SC/PARIS 2131</td>
          <td>15.0500</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>183</td>
          <td>0</td>
          <td>3</td>
          <td>Asplund, Master. Clarence Gustaf Hugo</td>
          <td>male</td>
          <td>9.00</td>
          <td>4</td>
          <td>2</td>
          <td>347077</td>
          <td>31.3875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>184</td>
          <td>1</td>
          <td>2</td>
          <td>Becker, Master. Richard F</td>
          <td>male</td>
          <td>1.00</td>
          <td>2</td>
          <td>1</td>
          <td>230136</td>
          <td>39.0000</td>
          <td>F4</td>
          <td>S</td>
        </tr>
        <tr>
          <td>185</td>
          <td>1</td>
          <td>3</td>
          <td>Kink-Heilmann, Miss. Luise Gretchen</td>
          <td>female</td>
          <td>4.00</td>
          <td>0</td>
          <td>2</td>
          <td>315153</td>
          <td>22.0250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>186</td>
          <td>0</td>
          <td>1</td>
          <td>Rood, Mr. Hugh Roscoe</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>113767</td>
          <td>50.0000</td>
          <td>A32</td>
          <td>S</td>
        </tr>
        <tr>
          <td>187</td>
          <td>1</td>
          <td>3</td>
          <td>O'Brien, Mrs. Thomas (Johanna "Hannah" Godfrey)</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>370365</td>
          <td>15.5000</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>188</td>
          <td>1</td>
          <td>1</td>
          <td>Romaine, Mr. Charles Hallace ("Mr C Rolmane")</td>
          <td>male</td>
          <td>45.00</td>
          <td>0</td>
          <td>0</td>
          <td>111428</td>
          <td>26.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>189</td>
          <td>0</td>
          <td>3</td>
          <td>Bourke, Mr. John</td>
          <td>male</td>
          <td>40.00</td>
          <td>1</td>
          <td>1</td>
          <td>364849</td>
          <td>15.5000</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>190</td>
          <td>0</td>
          <td>3</td>
          <td>Turcin, Mr. Stjepan</td>
          <td>male</td>
          <td>36.00</td>
          <td>0</td>
          <td>0</td>
          <td>349247</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>191</td>
          <td>1</td>
          <td>2</td>
          <td>Pinsky, Mrs. (Rosa)</td>
          <td>female</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>234604</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>192</td>
          <td>0</td>
          <td>2</td>
          <td>Carbines, Mr. William</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>28424</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>193</td>
          <td>1</td>
          <td>3</td>
          <td>Andersen-Jensen, Miss. Carla Christine Nielsine</td>
          <td>female</td>
          <td>19.00</td>
          <td>1</td>
          <td>0</td>
          <td>350046</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>194</td>
          <td>1</td>
          <td>2</td>
          <td>Navratil, Master. Michel M</td>
          <td>male</td>
          <td>3.00</td>
          <td>1</td>
          <td>1</td>
          <td>230080</td>
          <td>26.0000</td>
          <td>F2</td>
          <td>S</td>
        </tr>
        <tr>
          <td>195</td>
          <td>1</td>
          <td>1</td>
          <td>Brown, Mrs. James Joseph (Margaret Tobin)</td>
          <td>female</td>
          <td>44.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17610</td>
          <td>27.7208</td>
          <td>B4</td>
          <td>C</td>
        </tr>
        <tr>
          <td>196</td>
          <td>1</td>
          <td>1</td>
          <td>Lurette, Miss. Elise</td>
          <td>female</td>
          <td>58.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17569</td>
          <td>146.5208</td>
          <td>B80</td>
          <td>C</td>
        </tr>
        <tr>
          <td>197</td>
          <td>0</td>
          <td>3</td>
          <td>Mernagh, Mr. Robert</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>368703</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>198</td>
          <td>0</td>
          <td>3</td>
          <td>Olsen, Mr. Karl Siegwart Andreas</td>
          <td>male</td>
          <td>42.00</td>
          <td>0</td>
          <td>1</td>
          <td>4579</td>
          <td>8.4042</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>199</td>
          <td>1</td>
          <td>3</td>
          <td>Madigan, Miss. Margaret "Maggie"</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>370370</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>200</td>
          <td>0</td>
          <td>2</td>
          <td>Yrois, Miss. Henriette ("Mrs Harbeck")</td>
          <td>female</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>248747</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>201</td>
          <td>0</td>
          <td>3</td>
          <td>Vande Walle, Mr. Nestor Cyriel</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>345770</td>
          <td>9.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>202</td>
          <td>0</td>
          <td>3</td>
          <td>Sage, Mr. Frederick</td>
          <td>male</td>
          <td></td>
          <td>8</td>
          <td>2</td>
          <td>CA. 2343</td>
          <td>69.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>203</td>
          <td>0</td>
          <td>3</td>
          <td>Johanson, Mr. Jakob Alfred</td>
          <td>male</td>
          <td>34.00</td>
          <td>0</td>
          <td>0</td>
          <td>3101264</td>
          <td>6.4958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>204</td>
          <td>0</td>
          <td>3</td>
          <td>Youseff, Mr. Gerious</td>
          <td>male</td>
          <td>45.50</td>
          <td>0</td>
          <td>0</td>
          <td>2628</td>
          <td>7.2250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>205</td>
          <td>1</td>
          <td>3</td>
          <td>Cohen, Mr. Gurshon "Gus"</td>
          <td>male</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/5 3540</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>206</td>
          <td>0</td>
          <td>3</td>
          <td>Strom, Miss. Telma Matilda</td>
          <td>female</td>
          <td>2.00</td>
          <td>0</td>
          <td>1</td>
          <td>347054</td>
          <td>10.4625</td>
          <td>G6</td>
          <td>S</td>
        </tr>
        <tr>
          <td>207</td>
          <td>0</td>
          <td>3</td>
          <td>Backstrom, Mr. Karl Alfred</td>
          <td>male</td>
          <td>32.00</td>
          <td>1</td>
          <td>0</td>
          <td>3101278</td>
          <td>15.8500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>208</td>
          <td>1</td>
          <td>3</td>
          <td>Albimona, Mr. Nassef Cassem</td>
          <td>male</td>
          <td>26.00</td>
          <td>0</td>
          <td>0</td>
          <td>2699</td>
          <td>18.7875</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>209</td>
          <td>1</td>
          <td>3</td>
          <td>Carr, Miss. Helen "Ellen"</td>
          <td>female</td>
          <td>16.00</td>
          <td>0</td>
          <td>0</td>
          <td>367231</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>210</td>
          <td>1</td>
          <td>1</td>
          <td>Blank, Mr. Henry</td>
          <td>male</td>
          <td>40.00</td>
          <td>0</td>
          <td>0</td>
          <td>112277</td>
          <td>31.0000</td>
          <td>A31</td>
          <td>C</td>
        </tr>
        <tr>
          <td>211</td>
          <td>0</td>
          <td>3</td>
          <td>Ali, Mr. Ahmed</td>
          <td>male</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/O.Q. 3101311</td>
          <td>7.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>212</td>
          <td>1</td>
          <td>2</td>
          <td>Cameron, Miss. Clear Annie</td>
          <td>female</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>F.C.C. 13528</td>
          <td>21.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>213</td>
          <td>0</td>
          <td>3</td>
          <td>Perkin, Mr. John Henry</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/5 21174</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>214</td>
          <td>0</td>
          <td>2</td>
          <td>Givard, Mr. Hans Kristensen</td>
          <td>male</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>250646</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>215</td>
          <td>0</td>
          <td>3</td>
          <td>Kiernan, Mr. Philip</td>
          <td>male</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>367229</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>216</td>
          <td>1</td>
          <td>1</td>
          <td>Newell, Miss. Madeleine</td>
          <td>female</td>
          <td>31.00</td>
          <td>1</td>
          <td>0</td>
          <td>35273</td>
          <td>113.2750</td>
          <td>D36</td>
          <td>C</td>
        </tr>
        <tr>
          <td>217</td>
          <td>1</td>
          <td>3</td>
          <td>Honkanen, Miss. Eliina</td>
          <td>female</td>
          <td>27.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O2. 3101283</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>218</td>
          <td>0</td>
          <td>2</td>
          <td>Jacobsohn, Mr. Sidney Samuel</td>
          <td>male</td>
          <td>42.00</td>
          <td>1</td>
          <td>0</td>
          <td>243847</td>
          <td>27.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>219</td>
          <td>1</td>
          <td>1</td>
          <td>Bazzani, Miss. Albina</td>
          <td>female</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>11813</td>
          <td>76.2917</td>
          <td>D15</td>
          <td>C</td>
        </tr>
        <tr>
          <td>220</td>
          <td>0</td>
          <td>2</td>
          <td>Harris, Mr. Walter</td>
          <td>male</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>W/C 14208</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>221</td>
          <td>1</td>
          <td>3</td>
          <td>Sunderland, Mr. Victor Francis</td>
          <td>male</td>
          <td>16.00</td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/OQ 392089</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>222</td>
          <td>0</td>
          <td>2</td>
          <td>Bracken, Mr. James H</td>
          <td>male</td>
          <td>27.00</td>
          <td>0</td>
          <td>0</td>
          <td>220367</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>223</td>
          <td>0</td>
          <td>3</td>
          <td>Green, Mr. George Henry</td>
          <td>male</td>
          <td>51.00</td>
          <td>0</td>
          <td>0</td>
          <td>21440</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>224</td>
          <td>0</td>
          <td>3</td>
          <td>Nenkoff, Mr. Christo</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349234</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>225</td>
          <td>1</td>
          <td>1</td>
          <td>Hoyt, Mr. Frederick Maxfield</td>
          <td>male</td>
          <td>38.00</td>
          <td>1</td>
          <td>0</td>
          <td>19943</td>
          <td>90.0000</td>
          <td>C93</td>
          <td>S</td>
        </tr>
        <tr>
          <td>226</td>
          <td>0</td>
          <td>3</td>
          <td>Berglund, Mr. Karl Ivar Sven</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>PP 4348</td>
          <td>9.3500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>227</td>
          <td>1</td>
          <td>2</td>
          <td>Mellors, Mr. William John</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>SW/PP 751</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>228</td>
          <td>0</td>
          <td>3</td>
          <td>Lovell, Mr. John Hall ("Henry")</td>
          <td>male</td>
          <td>20.50</td>
          <td>0</td>
          <td>0</td>
          <td>A/5 21173</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>229</td>
          <td>0</td>
          <td>2</td>
          <td>Fahlstrom, Mr. Arne Jonas</td>
          <td>male</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>236171</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>230</td>
          <td>0</td>
          <td>3</td>
          <td>Lefebre, Miss. Mathilde</td>
          <td>female</td>
          <td></td>
          <td>3</td>
          <td>1</td>
          <td>4133</td>
          <td>25.4667</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>231</td>
          <td>1</td>
          <td>1</td>
          <td>Harris, Mrs. Henry Birkhardt (Irene Wallach)</td>
          <td>female</td>
          <td>35.00</td>
          <td>1</td>
          <td>0</td>
          <td>36973</td>
          <td>83.4750</td>
          <td>C83</td>
          <td>S</td>
        </tr>
        <tr>
          <td>232</td>
          <td>0</td>
          <td>3</td>
          <td>Larsson, Mr. Bengt Edvin</td>
          <td>male</td>
          <td>29.00</td>
          <td>0</td>
          <td>0</td>
          <td>347067</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>233</td>
          <td>0</td>
          <td>2</td>
          <td>Sjostedt, Mr. Ernst Adolf</td>
          <td>male</td>
          <td>59.00</td>
          <td>0</td>
          <td>0</td>
          <td>237442</td>
          <td>13.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>234</td>
          <td>1</td>
          <td>3</td>
          <td>Asplund, Miss. Lillian Gertrud</td>
          <td>female</td>
          <td>5.00</td>
          <td>4</td>
          <td>2</td>
          <td>347077</td>
          <td>31.3875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>235</td>
          <td>0</td>
          <td>2</td>
          <td>Leyson, Mr. Robert William Norman</td>
          <td>male</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 29566</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>236</td>
          <td>0</td>
          <td>3</td>
          <td>Harknett, Miss. Alice Phoebe</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>W./C. 6609</td>
          <td>7.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>237</td>
          <td>0</td>
          <td>2</td>
          <td>Hold, Mr. Stephen</td>
          <td>male</td>
          <td>44.00</td>
          <td>1</td>
          <td>0</td>
          <td>26707</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>238</td>
          <td>1</td>
          <td>2</td>
          <td>Collyer, Miss. Marjorie "Lottie"</td>
          <td>female</td>
          <td>8.00</td>
          <td>0</td>
          <td>2</td>
          <td>C.A. 31921</td>
          <td>26.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>239</td>
          <td>0</td>
          <td>2</td>
          <td>Pengelly, Mr. Frederick William</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>28665</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>240</td>
          <td>0</td>
          <td>2</td>
          <td>Hunt, Mr. George Henry</td>
          <td>male</td>
          <td>33.00</td>
          <td>0</td>
          <td>0</td>
          <td>SCO/W 1585</td>
          <td>12.2750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>241</td>
          <td>0</td>
          <td>3</td>
          <td>Zabour, Miss. Thamine</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>2665</td>
          <td>14.4542</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>242</td>
          <td>1</td>
          <td>3</td>
          <td>Murphy, Miss. Katherine "Kate"</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>367230</td>
          <td>15.5000</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>243</td>
          <td>0</td>
          <td>2</td>
          <td>Coleridge, Mr. Reginald Charles</td>
          <td>male</td>
          <td>29.00</td>
          <td>0</td>
          <td>0</td>
          <td>W./C. 14263</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>244</td>
          <td>0</td>
          <td>3</td>
          <td>Maenpaa, Mr. Matti Alexanteri</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O 2. 3101275</td>
          <td>7.1250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>245</td>
          <td>0</td>
          <td>3</td>
          <td>Attalah, Mr. Sleiman</td>
          <td>male</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>2694</td>
          <td>7.2250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>246</td>
          <td>0</td>
          <td>1</td>
          <td>Minahan, Dr. William Edward</td>
          <td>male</td>
          <td>44.00</td>
          <td>2</td>
          <td>0</td>
          <td>19928</td>
          <td>90.0000</td>
          <td>C78</td>
          <td>Q</td>
        </tr>
        <tr>
          <td>247</td>
          <td>0</td>
          <td>3</td>
          <td>Lindahl, Miss. Agda Thorilda Viktoria</td>
          <td>female</td>
          <td>25.00</td>
          <td>0</td>
          <td>0</td>
          <td>347071</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>248</td>
          <td>1</td>
          <td>2</td>
          <td>Hamalainen, Mrs. William (Anna)</td>
          <td>female</td>
          <td>24.00</td>
          <td>0</td>
          <td>2</td>
          <td>250649</td>
          <td>14.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>249</td>
          <td>1</td>
          <td>1</td>
          <td>Beckwith, Mr. Richard Leonard</td>
          <td>male</td>
          <td>37.00</td>
          <td>1</td>
          <td>1</td>
          <td>11751</td>
          <td>52.5542</td>
          <td>D35</td>
          <td>S</td>
        </tr>
        <tr>
          <td>250</td>
          <td>0</td>
          <td>2</td>
          <td>Carter, Rev. Ernest Courtenay</td>
          <td>male</td>
          <td>54.00</td>
          <td>1</td>
          <td>0</td>
          <td>244252</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>251</td>
          <td>0</td>
          <td>3</td>
          <td>Reed, Mr. James George</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>362316</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>252</td>
          <td>0</td>
          <td>3</td>
          <td>Strom, Mrs. Wilhelm (Elna Matilda Persson)</td>
          <td>female</td>
          <td>29.00</td>
          <td>1</td>
          <td>1</td>
          <td>347054</td>
          <td>10.4625</td>
          <td>G6</td>
          <td>S</td>
        </tr>
        <tr>
          <td>253</td>
          <td>0</td>
          <td>1</td>
          <td>Stead, Mr. William Thomas</td>
          <td>male</td>
          <td>62.00</td>
          <td>0</td>
          <td>0</td>
          <td>113514</td>
          <td>26.5500</td>
          <td>C87</td>
          <td>S</td>
        </tr>
        <tr>
          <td>254</td>
          <td>0</td>
          <td>3</td>
          <td>Lobb, Mr. William Arthur</td>
          <td>male</td>
          <td>30.00</td>
          <td>1</td>
          <td>0</td>
          <td>A/5. 3336</td>
          <td>16.1000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>255</td>
          <td>0</td>
          <td>3</td>
          <td>Rosblom, Mrs. Viktor (Helena Wilhelmina)</td>
          <td>female</td>
          <td>41.00</td>
          <td>0</td>
          <td>2</td>
          <td>370129</td>
          <td>20.2125</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>256</td>
          <td>1</td>
          <td>3</td>
          <td>Touma, Mrs. Darwis (Hanne Youssef Razi)</td>
          <td>female</td>
          <td>29.00</td>
          <td>0</td>
          <td>2</td>
          <td>2650</td>
          <td>15.2458</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>257</td>
          <td>1</td>
          <td>1</td>
          <td>Thorne, Mrs. Gertrude Maybelle</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>PC 17585</td>
          <td>79.2000</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>258</td>
          <td>1</td>
          <td>1</td>
          <td>Cherry, Miss. Gladys</td>
          <td>female</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>110152</td>
          <td>86.5000</td>
          <td>B77</td>
          <td>S</td>
        </tr>
        <tr>
          <td>259</td>
          <td>1</td>
          <td>1</td>
          <td>Ward, Miss. Anna</td>
          <td>female</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17755</td>
          <td>512.3292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>260</td>
          <td>1</td>
          <td>2</td>
          <td>Parrish, Mrs. (Lutie Davis)</td>
          <td>female</td>
          <td>50.00</td>
          <td>0</td>
          <td>1</td>
          <td>230433</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>261</td>
          <td>0</td>
          <td>3</td>
          <td>Smith, Mr. Thomas</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>384461</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>262</td>
          <td>1</td>
          <td>3</td>
          <td>Asplund, Master. Edvin Rojj Felix</td>
          <td>male</td>
          <td>3.00</td>
          <td>4</td>
          <td>2</td>
          <td>347077</td>
          <td>31.3875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>263</td>
          <td>0</td>
          <td>1</td>
          <td>Taussig, Mr. Emil</td>
          <td>male</td>
          <td>52.00</td>
          <td>1</td>
          <td>1</td>
          <td>110413</td>
          <td>79.6500</td>
          <td>E67</td>
          <td>S</td>
        </tr>
        <tr>
          <td>264</td>
          <td>0</td>
          <td>1</td>
          <td>Harrison, Mr. William</td>
          <td>male</td>
          <td>40.00</td>
          <td>0</td>
          <td>0</td>
          <td>112059</td>
          <td>0.0000</td>
          <td>B94</td>
          <td>S</td>
        </tr>
        <tr>
          <td>265</td>
          <td>0</td>
          <td>3</td>
          <td>Henry, Miss. Delia</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>382649</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>266</td>
          <td>0</td>
          <td>2</td>
          <td>Reeves, Mr. David</td>
          <td>male</td>
          <td>36.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 17248</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>267</td>
          <td>0</td>
          <td>3</td>
          <td>Panula, Mr. Ernesti Arvid</td>
          <td>male</td>
          <td>16.00</td>
          <td>4</td>
          <td>1</td>
          <td>3101295</td>
          <td>39.6875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>268</td>
          <td>1</td>
          <td>3</td>
          <td>Persson, Mr. Ernst Ulrik</td>
          <td>male</td>
          <td>25.00</td>
          <td>1</td>
          <td>0</td>
          <td>347083</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>269</td>
          <td>1</td>
          <td>1</td>
          <td>Graham, Mrs. William Thompson (Edith Junkins)</td>
          <td>female</td>
          <td>58.00</td>
          <td>0</td>
          <td>1</td>
          <td>PC 17582</td>
          <td>153.4625</td>
          <td>C125</td>
          <td>S</td>
        </tr>
        <tr>
          <td>270</td>
          <td>1</td>
          <td>1</td>
          <td>Bissette, Miss. Amelia</td>
          <td>female</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17760</td>
          <td>135.6333</td>
          <td>C99</td>
          <td>S</td>
        </tr>
        <tr>
          <td>271</td>
          <td>0</td>
          <td>1</td>
          <td>Cairns, Mr. Alexander</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>113798</td>
          <td>31.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>272</td>
          <td>1</td>
          <td>3</td>
          <td>Tornquist, Mr. William Henry</td>
          <td>male</td>
          <td>25.00</td>
          <td>0</td>
          <td>0</td>
          <td>LINE</td>
          <td>0.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>273</td>
          <td>1</td>
          <td>2</td>
          <td>Mellinger, Mrs. (Elizabeth Anne Maidment)</td>
          <td>female</td>
          <td>41.00</td>
          <td>0</td>
          <td>1</td>
          <td>250644</td>
          <td>19.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>274</td>
          <td>0</td>
          <td>1</td>
          <td>Natsch, Mr. Charles H</td>
          <td>male</td>
          <td>37.00</td>
          <td>0</td>
          <td>1</td>
          <td>PC 17596</td>
          <td>29.7000</td>
          <td>C118</td>
          <td>C</td>
        </tr>
        <tr>
          <td>275</td>
          <td>1</td>
          <td>3</td>
          <td>Healy, Miss. Hanora "Nora"</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>370375</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>276</td>
          <td>1</td>
          <td>1</td>
          <td>Andrews, Miss. Kornelia Theodosia</td>
          <td>female</td>
          <td>63.00</td>
          <td>1</td>
          <td>0</td>
          <td>13502</td>
          <td>77.9583</td>
          <td>D7</td>
          <td>S</td>
        </tr>
        <tr>
          <td>277</td>
          <td>0</td>
          <td>3</td>
          <td>Lindblom, Miss. Augusta Charlotta</td>
          <td>female</td>
          <td>45.00</td>
          <td>0</td>
          <td>0</td>
          <td>347073</td>
          <td>7.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>278</td>
          <td>0</td>
          <td>2</td>
          <td>Parkes, Mr. Francis "Frank"</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>239853</td>
          <td>0.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>279</td>
          <td>0</td>
          <td>3</td>
          <td>Rice, Master. Eric</td>
          <td>male</td>
          <td>7.00</td>
          <td>4</td>
          <td>1</td>
          <td>382652</td>
          <td>29.1250</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>280</td>
          <td>1</td>
          <td>3</td>
          <td>Abbott, Mrs. Stanton (Rosa Hunt)</td>
          <td>female</td>
          <td>35.00</td>
          <td>1</td>
          <td>1</td>
          <td>C.A. 2673</td>
          <td>20.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>281</td>
          <td>0</td>
          <td>3</td>
          <td>Duane, Mr. Frank</td>
          <td>male</td>
          <td>65.00</td>
          <td>0</td>
          <td>0</td>
          <td>336439</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>282</td>
          <td>0</td>
          <td>3</td>
          <td>Olsson, Mr. Nils Johan Goransson</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>347464</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>283</td>
          <td>0</td>
          <td>3</td>
          <td>de Pelsmaeker, Mr. Alfons</td>
          <td>male</td>
          <td>16.00</td>
          <td>0</td>
          <td>0</td>
          <td>345778</td>
          <td>9.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>284</td>
          <td>1</td>
          <td>3</td>
          <td>Dorking, Mr. Edward Arthur</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/5. 10482</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>285</td>
          <td>0</td>
          <td>1</td>
          <td>Smith, Mr. Richard William</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>113056</td>
          <td>26.0000</td>
          <td>A19</td>
          <td>S</td>
        </tr>
        <tr>
          <td>286</td>
          <td>0</td>
          <td>3</td>
          <td>Stankovic, Mr. Ivan</td>
          <td>male</td>
          <td>33.00</td>
          <td>0</td>
          <td>0</td>
          <td>349239</td>
          <td>8.6625</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>287</td>
          <td>1</td>
          <td>3</td>
          <td>de Mulder, Mr. Theodore</td>
          <td>male</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>345774</td>
          <td>9.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>288</td>
          <td>0</td>
          <td>3</td>
          <td>Naidenoff, Mr. Penko</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>349206</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>289</td>
          <td>1</td>
          <td>2</td>
          <td>Hosono, Mr. Masabumi</td>
          <td>male</td>
          <td>42.00</td>
          <td>0</td>
          <td>0</td>
          <td>237798</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>290</td>
          <td>1</td>
          <td>3</td>
          <td>Connolly, Miss. Kate</td>
          <td>female</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>370373</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>291</td>
          <td>1</td>
          <td>1</td>
          <td>Barber, Miss. Ellen "Nellie"</td>
          <td>female</td>
          <td>26.00</td>
          <td>0</td>
          <td>0</td>
          <td>19877</td>
          <td>78.8500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>292</td>
          <td>1</td>
          <td>1</td>
          <td>Bishop, Mrs. Dickinson H (Helen Walton)</td>
          <td>female</td>
          <td>19.00</td>
          <td>1</td>
          <td>0</td>
          <td>11967</td>
          <td>91.0792</td>
          <td>B49</td>
          <td>C</td>
        </tr>
        <tr>
          <td>293</td>
          <td>0</td>
          <td>2</td>
          <td>Levy, Mr. Rene Jacques</td>
          <td>male</td>
          <td>36.00</td>
          <td>0</td>
          <td>0</td>
          <td>SC/Paris 2163</td>
          <td>12.8750</td>
          <td>D</td>
          <td>C</td>
        </tr>
        <tr>
          <td>294</td>
          <td>0</td>
          <td>3</td>
          <td>Haas, Miss. Aloisia</td>
          <td>female</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>349236</td>
          <td>8.8500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>295</td>
          <td>0</td>
          <td>3</td>
          <td>Mineff, Mr. Ivan</td>
          <td>male</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>349233</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>296</td>
          <td>0</td>
          <td>1</td>
          <td>Lewy, Mr. Ervin G</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>PC 17612</td>
          <td>27.7208</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>297</td>
          <td>0</td>
          <td>3</td>
          <td>Hanna, Mr. Mansour</td>
          <td>male</td>
          <td>23.50</td>
          <td>0</td>
          <td>0</td>
          <td>2693</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>298</td>
          <td>0</td>
          <td>1</td>
          <td>Allison, Miss. Helen Loraine</td>
          <td>female</td>
          <td>2.00</td>
          <td>1</td>
          <td>2</td>
          <td>113781</td>
          <td>151.5500</td>
          <td>C22 C26</td>
          <td>S</td>
        </tr>
        <tr>
          <td>299</td>
          <td>1</td>
          <td>1</td>
          <td>Saalfeld, Mr. Adolphe</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>19988</td>
          <td>30.5000</td>
          <td>C106</td>
          <td>S</td>
        </tr>
        <tr>
          <td>300</td>
          <td>1</td>
          <td>1</td>
          <td>Baxter, Mrs. James (Helene DeLaudeniere Chaput)</td>
          <td>female</td>
          <td>50.00</td>
          <td>0</td>
          <td>1</td>
          <td>PC 17558</td>
          <td>247.5208</td>
          <td>B58 B60</td>
          <td>C</td>
        </tr>
        <tr>
          <td>301</td>
          <td>1</td>
          <td>3</td>
          <td>Kelly, Miss. Anna Katherine "Annie Kate"</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>9234</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>302</td>
          <td>1</td>
          <td>3</td>
          <td>McCoy, Mr. Bernard</td>
          <td>male</td>
          <td></td>
          <td>2</td>
          <td>0</td>
          <td>367226</td>
          <td>23.2500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>303</td>
          <td>0</td>
          <td>3</td>
          <td>Johnson, Mr. William Cahoone Jr</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>LINE</td>
          <td>0.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>304</td>
          <td>1</td>
          <td>2</td>
          <td>Keane, Miss. Nora A</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>226593</td>
          <td>12.3500</td>
          <td>E101</td>
          <td>Q</td>
        </tr>
        <tr>
          <td>305</td>
          <td>0</td>
          <td>3</td>
          <td>Williams, Mr. Howard Hugh "Harry"</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>A/5 2466</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>306</td>
          <td>1</td>
          <td>1</td>
          <td>Allison, Master. Hudson Trevor</td>
          <td>male</td>
          <td>0.92</td>
          <td>1</td>
          <td>2</td>
          <td>113781</td>
          <td>151.5500</td>
          <td>C22 C26</td>
          <td>S</td>
        </tr>
        <tr>
          <td>307</td>
          <td>1</td>
          <td>1</td>
          <td>Fleming, Miss. Margaret</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>17421</td>
          <td>110.8833</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>308</td>
          <td>1</td>
          <td>1</td>
          <td>Penasco y Castellana, Mrs. Victor de Satode (Maria Josefa Perez de Soto y Vallejo)</td>
          <td>female</td>
          <td>17.00</td>
          <td>1</td>
          <td>0</td>
          <td>PC 17758</td>
          <td>108.9000</td>
          <td>C65</td>
          <td>C</td>
        </tr>
        <tr>
          <td>309</td>
          <td>0</td>
          <td>2</td>
          <td>Abelson, Mr. Samuel</td>
          <td>male</td>
          <td>30.00</td>
          <td>1</td>
          <td>0</td>
          <td>P/PP 3381</td>
          <td>24.0000</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>310</td>
          <td>1</td>
          <td>1</td>
          <td>Francatelli, Miss. Laura Mabel</td>
          <td>female</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17485</td>
          <td>56.9292</td>
          <td>E36</td>
          <td>C</td>
        </tr>
        <tr>
          <td>311</td>
          <td>1</td>
          <td>1</td>
          <td>Hays, Miss. Margaret Bechstein</td>
          <td>female</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>11767</td>
          <td>83.1583</td>
          <td>C54</td>
          <td>C</td>
        </tr>
        <tr>
          <td>312</td>
          <td>1</td>
          <td>1</td>
          <td>Ryerson, Miss. Emily Borie</td>
          <td>female</td>
          <td>18.00</td>
          <td>2</td>
          <td>2</td>
          <td>PC 17608</td>
          <td>262.3750</td>
          <td>B57 B59 B63 B66</td>
          <td>C</td>
        </tr>
        <tr>
          <td>313</td>
          <td>0</td>
          <td>2</td>
          <td>Lahtinen, Mrs. William (Anna Sylfven)</td>
          <td>female</td>
          <td>26.00</td>
          <td>1</td>
          <td>1</td>
          <td>250651</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>314</td>
          <td>0</td>
          <td>3</td>
          <td>Hendekovic, Mr. Ignjac</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>349243</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>315</td>
          <td>0</td>
          <td>2</td>
          <td>Hart, Mr. Benjamin</td>
          <td>male</td>
          <td>43.00</td>
          <td>1</td>
          <td>1</td>
          <td>F.C.C. 13529</td>
          <td>26.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>316</td>
          <td>1</td>
          <td>3</td>
          <td>Nilsson, Miss. Helmina Josefina</td>
          <td>female</td>
          <td>26.00</td>
          <td>0</td>
          <td>0</td>
          <td>347470</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>317</td>
          <td>1</td>
          <td>2</td>
          <td>Kantor, Mrs. Sinai (Miriam Sternin)</td>
          <td>female</td>
          <td>24.00</td>
          <td>1</td>
          <td>0</td>
          <td>244367</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>318</td>
          <td>0</td>
          <td>2</td>
          <td>Moraweck, Dr. Ernest</td>
          <td>male</td>
          <td>54.00</td>
          <td>0</td>
          <td>0</td>
          <td>29011</td>
          <td>14.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>319</td>
          <td>1</td>
          <td>1</td>
          <td>Wick, Miss. Mary Natalie</td>
          <td>female</td>
          <td>31.00</td>
          <td>0</td>
          <td>2</td>
          <td>36928</td>
          <td>164.8667</td>
          <td>C7</td>
          <td>S</td>
        </tr>
        <tr>
          <td>320</td>
          <td>1</td>
          <td>1</td>
          <td>Spedden, Mrs. Frederic Oakley (Margaretta Corning Stone)</td>
          <td>female</td>
          <td>40.00</td>
          <td>1</td>
          <td>1</td>
          <td>16966</td>
          <td>134.5000</td>
          <td>E34</td>
          <td>C</td>
        </tr>
        <tr>
          <td>321</td>
          <td>0</td>
          <td>3</td>
          <td>Dennis, Mr. Samuel</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/5 21172</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>322</td>
          <td>0</td>
          <td>3</td>
          <td>Danoff, Mr. Yoto</td>
          <td>male</td>
          <td>27.00</td>
          <td>0</td>
          <td>0</td>
          <td>349219</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>323</td>
          <td>1</td>
          <td>2</td>
          <td>Slayter, Miss. Hilda Mary</td>
          <td>female</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>234818</td>
          <td>12.3500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>324</td>
          <td>1</td>
          <td>2</td>
          <td>Caldwell, Mrs. Albert Francis (Sylvia Mae Harbaugh)</td>
          <td>female</td>
          <td>22.00</td>
          <td>1</td>
          <td>1</td>
          <td>248738</td>
          <td>29.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>325</td>
          <td>0</td>
          <td>3</td>
          <td>Sage, Mr. George John Jr</td>
          <td>male</td>
          <td></td>
          <td>8</td>
          <td>2</td>
          <td>CA. 2343</td>
          <td>69.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>326</td>
          <td>1</td>
          <td>1</td>
          <td>Young, Miss. Marie Grice</td>
          <td>female</td>
          <td>36.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17760</td>
          <td>135.6333</td>
          <td>C32</td>
          <td>C</td>
        </tr>
        <tr>
          <td>327</td>
          <td>0</td>
          <td>3</td>
          <td>Nysveen, Mr. Johan Hansen</td>
          <td>male</td>
          <td>61.00</td>
          <td>0</td>
          <td>0</td>
          <td>345364</td>
          <td>6.2375</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>328</td>
          <td>1</td>
          <td>2</td>
          <td>Ball, Mrs. (Ada E Hall)</td>
          <td>female</td>
          <td>36.00</td>
          <td>0</td>
          <td>0</td>
          <td>28551</td>
          <td>13.0000</td>
          <td>D</td>
          <td>S</td>
        </tr>
        <tr>
          <td>329</td>
          <td>1</td>
          <td>3</td>
          <td>Goldsmith, Mrs. Frank John (Emily Alice Brown)</td>
          <td>female</td>
          <td>31.00</td>
          <td>1</td>
          <td>1</td>
          <td>363291</td>
          <td>20.5250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>330</td>
          <td>1</td>
          <td>1</td>
          <td>Hippach, Miss. Jean Gertrude</td>
          <td>female</td>
          <td>16.00</td>
          <td>0</td>
          <td>1</td>
          <td>111361</td>
          <td>57.9792</td>
          <td>B18</td>
          <td>C</td>
        </tr>
        <tr>
          <td>331</td>
          <td>1</td>
          <td>3</td>
          <td>McCoy, Miss. Agnes</td>
          <td>female</td>
          <td></td>
          <td>2</td>
          <td>0</td>
          <td>367226</td>
          <td>23.2500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>332</td>
          <td>0</td>
          <td>1</td>
          <td>Partner, Mr. Austen</td>
          <td>male</td>
          <td>45.50</td>
          <td>0</td>
          <td>0</td>
          <td>113043</td>
          <td>28.5000</td>
          <td>C124</td>
          <td>S</td>
        </tr>
        <tr>
          <td>333</td>
          <td>0</td>
          <td>1</td>
          <td>Graham, Mr. George Edward</td>
          <td>male</td>
          <td>38.00</td>
          <td>0</td>
          <td>1</td>
          <td>PC 17582</td>
          <td>153.4625</td>
          <td>C91</td>
          <td>S</td>
        </tr>
        <tr>
          <td>334</td>
          <td>0</td>
          <td>3</td>
          <td>Vander Planke, Mr. Leo Edmondus</td>
          <td>male</td>
          <td>16.00</td>
          <td>2</td>
          <td>0</td>
          <td>345764</td>
          <td>18.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>335</td>
          <td>1</td>
          <td>1</td>
          <td>Frauenthal, Mrs. Henry William (Clara Heinsheimer)</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>PC 17611</td>
          <td>133.6500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>336</td>
          <td>0</td>
          <td>3</td>
          <td>Denkoff, Mr. Mitto</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349225</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>337</td>
          <td>0</td>
          <td>1</td>
          <td>Pears, Mr. Thomas Clinton</td>
          <td>male</td>
          <td>29.00</td>
          <td>1</td>
          <td>0</td>
          <td>113776</td>
          <td>66.6000</td>
          <td>C2</td>
          <td>S</td>
        </tr>
        <tr>
          <td>338</td>
          <td>1</td>
          <td>1</td>
          <td>Burns, Miss. Elizabeth Margaret</td>
          <td>female</td>
          <td>41.00</td>
          <td>0</td>
          <td>0</td>
          <td>16966</td>
          <td>134.5000</td>
          <td>E40</td>
          <td>C</td>
        </tr>
        <tr>
          <td>339</td>
          <td>1</td>
          <td>3</td>
          <td>Dahl, Mr. Karl Edwart</td>
          <td>male</td>
          <td>45.00</td>
          <td>0</td>
          <td>0</td>
          <td>7598</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>340</td>
          <td>0</td>
          <td>1</td>
          <td>Blackwell, Mr. Stephen Weart</td>
          <td>male</td>
          <td>45.00</td>
          <td>0</td>
          <td>0</td>
          <td>113784</td>
          <td>35.5000</td>
          <td>T</td>
          <td>S</td>
        </tr>
        <tr>
          <td>341</td>
          <td>1</td>
          <td>2</td>
          <td>Navratil, Master. Edmond Roger</td>
          <td>male</td>
          <td>2.00</td>
          <td>1</td>
          <td>1</td>
          <td>230080</td>
          <td>26.0000</td>
          <td>F2</td>
          <td>S</td>
        </tr>
        <tr>
          <td>342</td>
          <td>1</td>
          <td>1</td>
          <td>Fortune, Miss. Alice Elizabeth</td>
          <td>female</td>
          <td>24.00</td>
          <td>3</td>
          <td>2</td>
          <td>19950</td>
          <td>263.0000</td>
          <td>C23 C25 C27</td>
          <td>S</td>
        </tr>
        <tr>
          <td>343</td>
          <td>0</td>
          <td>2</td>
          <td>Collander, Mr. Erik Gustaf</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>248740</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>344</td>
          <td>0</td>
          <td>2</td>
          <td>Sedgwick, Mr. Charles Frederick Waddington</td>
          <td>male</td>
          <td>25.00</td>
          <td>0</td>
          <td>0</td>
          <td>244361</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>345</td>
          <td>0</td>
          <td>2</td>
          <td>Fox, Mr. Stanley Hubert</td>
          <td>male</td>
          <td>36.00</td>
          <td>0</td>
          <td>0</td>
          <td>229236</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>346</td>
          <td>1</td>
          <td>2</td>
          <td>Brown, Miss. Amelia "Mildred"</td>
          <td>female</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>248733</td>
          <td>13.0000</td>
          <td>F33</td>
          <td>S</td>
        </tr>
        <tr>
          <td>347</td>
          <td>1</td>
          <td>2</td>
          <td>Smith, Miss. Marion Elsie</td>
          <td>female</td>
          <td>40.00</td>
          <td>0</td>
          <td>0</td>
          <td>31418</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>348</td>
          <td>1</td>
          <td>3</td>
          <td>Davison, Mrs. Thomas Henry (Mary E Finck)</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>386525</td>
          <td>16.1000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>349</td>
          <td>1</td>
          <td>3</td>
          <td>Coutts, Master. William Loch "William"</td>
          <td>male</td>
          <td>3.00</td>
          <td>1</td>
          <td>1</td>
          <td>C.A. 37671</td>
          <td>15.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>350</td>
          <td>0</td>
          <td>3</td>
          <td>Dimic, Mr. Jovan</td>
          <td>male</td>
          <td>42.00</td>
          <td>0</td>
          <td>0</td>
          <td>315088</td>
          <td>8.6625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>351</td>
          <td>0</td>
          <td>3</td>
          <td>Odahl, Mr. Nils Martin</td>
          <td>male</td>
          <td>23.00</td>
          <td>0</td>
          <td>0</td>
          <td>7267</td>
          <td>9.2250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>352</td>
          <td>0</td>
          <td>1</td>
          <td>Williams-Lambert, Mr. Fletcher Fellows</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>113510</td>
          <td>35.0000</td>
          <td>C128</td>
          <td>S</td>
        </tr>
        <tr>
          <td>353</td>
          <td>0</td>
          <td>3</td>
          <td>Elias, Mr. Tannous</td>
          <td>male</td>
          <td>15.00</td>
          <td>1</td>
          <td>1</td>
          <td>2695</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>354</td>
          <td>0</td>
          <td>3</td>
          <td>Arnold-Franchi, Mr. Josef</td>
          <td>male</td>
          <td>25.00</td>
          <td>1</td>
          <td>0</td>
          <td>349237</td>
          <td>17.8000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>355</td>
          <td>0</td>
          <td>3</td>
          <td>Yousif, Mr. Wazli</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2647</td>
          <td>7.2250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>356</td>
          <td>0</td>
          <td>3</td>
          <td>Vanden Steen, Mr. Leo Peter</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>345783</td>
          <td>9.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>357</td>
          <td>1</td>
          <td>1</td>
          <td>Bowerman, Miss. Elsie Edith</td>
          <td>female</td>
          <td>22.00</td>
          <td>0</td>
          <td>1</td>
          <td>113505</td>
          <td>55.0000</td>
          <td>E33</td>
          <td>S</td>
        </tr>
        <tr>
          <td>358</td>
          <td>0</td>
          <td>2</td>
          <td>Funk, Miss. Annie Clemmer</td>
          <td>female</td>
          <td>38.00</td>
          <td>0</td>
          <td>0</td>
          <td>237671</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>359</td>
          <td>1</td>
          <td>3</td>
          <td>McGovern, Miss. Mary</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>330931</td>
          <td>7.8792</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>360</td>
          <td>1</td>
          <td>3</td>
          <td>Mockler, Miss. Helen Mary "Ellie"</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>330980</td>
          <td>7.8792</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>361</td>
          <td>0</td>
          <td>3</td>
          <td>Skoog, Mr. Wilhelm</td>
          <td>male</td>
          <td>40.00</td>
          <td>1</td>
          <td>4</td>
          <td>347088</td>
          <td>27.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>362</td>
          <td>0</td>
          <td>2</td>
          <td>del Carlo, Mr. Sebastiano</td>
          <td>male</td>
          <td>29.00</td>
          <td>1</td>
          <td>0</td>
          <td>SC/PARIS 2167</td>
          <td>27.7208</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>363</td>
          <td>0</td>
          <td>3</td>
          <td>Barbara, Mrs. (Catherine David)</td>
          <td>female</td>
          <td>45.00</td>
          <td>0</td>
          <td>1</td>
          <td>2691</td>
          <td>14.4542</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>364</td>
          <td>0</td>
          <td>3</td>
          <td>Asim, Mr. Adola</td>
          <td>male</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/O.Q. 3101310</td>
          <td>7.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>365</td>
          <td>0</td>
          <td>3</td>
          <td>O'Brien, Mr. Thomas</td>
          <td>male</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>370365</td>
          <td>15.5000</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>366</td>
          <td>0</td>
          <td>3</td>
          <td>Adahl, Mr. Mauritz Nils Martin</td>
          <td>male</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>C 7076</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>367</td>
          <td>1</td>
          <td>1</td>
          <td>Warren, Mrs. Frank Manley (Anna Sophia Atkinson)</td>
          <td>female</td>
          <td>60.00</td>
          <td>1</td>
          <td>0</td>
          <td>110813</td>
          <td>75.2500</td>
          <td>D37</td>
          <td>C</td>
        </tr>
        <tr>
          <td>368</td>
          <td>1</td>
          <td>3</td>
          <td>Moussa, Mrs. (Mantoura Boulos)</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2626</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>369</td>
          <td>1</td>
          <td>3</td>
          <td>Jermyn, Miss. Annie</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>14313</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>370</td>
          <td>1</td>
          <td>1</td>
          <td>Aubart, Mme. Leontine Pauline</td>
          <td>female</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17477</td>
          <td>69.3000</td>
          <td>B35</td>
          <td>C</td>
        </tr>
        <tr>
          <td>371</td>
          <td>1</td>
          <td>1</td>
          <td>Harder, Mr. George Achilles</td>
          <td>male</td>
          <td>25.00</td>
          <td>1</td>
          <td>0</td>
          <td>11765</td>
          <td>55.4417</td>
          <td>E50</td>
          <td>C</td>
        </tr>
        <tr>
          <td>372</td>
          <td>0</td>
          <td>3</td>
          <td>Wiklund, Mr. Jakob Alfred</td>
          <td>male</td>
          <td>18.00</td>
          <td>1</td>
          <td>0</td>
          <td>3101267</td>
          <td>6.4958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>373</td>
          <td>0</td>
          <td>3</td>
          <td>Beavan, Mr. William Thomas</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>323951</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>374</td>
          <td>0</td>
          <td>1</td>
          <td>Ringhini, Mr. Sante</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17760</td>
          <td>135.6333</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>375</td>
          <td>0</td>
          <td>3</td>
          <td>Palsson, Miss. Stina Viola</td>
          <td>female</td>
          <td>3.00</td>
          <td>3</td>
          <td>1</td>
          <td>349909</td>
          <td>21.0750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>376</td>
          <td>1</td>
          <td>1</td>
          <td>Meyer, Mrs. Edgar Joseph (Leila Saks)</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>PC 17604</td>
          <td>82.1708</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>377</td>
          <td>1</td>
          <td>3</td>
          <td>Landergren, Miss. Aurora Adelia</td>
          <td>female</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>C 7077</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>378</td>
          <td>0</td>
          <td>1</td>
          <td>Widener, Mr. Harry Elkins</td>
          <td>male</td>
          <td>27.00</td>
          <td>0</td>
          <td>2</td>
          <td>113503</td>
          <td>211.5000</td>
          <td>C82</td>
          <td>C</td>
        </tr>
        <tr>
          <td>379</td>
          <td>0</td>
          <td>3</td>
          <td>Betros, Mr. Tannous</td>
          <td>male</td>
          <td>20.00</td>
          <td>0</td>
          <td>0</td>
          <td>2648</td>
          <td>4.0125</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>380</td>
          <td>0</td>
          <td>3</td>
          <td>Gustafsson, Mr. Karl Gideon</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>347069</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>381</td>
          <td>1</td>
          <td>1</td>
          <td>Bidois, Miss. Rosalie</td>
          <td>female</td>
          <td>42.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17757</td>
          <td>227.5250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>382</td>
          <td>1</td>
          <td>3</td>
          <td>Nakid, Miss. Maria ("Mary")</td>
          <td>female</td>
          <td>1.00</td>
          <td>0</td>
          <td>2</td>
          <td>2653</td>
          <td>15.7417</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>383</td>
          <td>0</td>
          <td>3</td>
          <td>Tikkanen, Mr. Juho</td>
          <td>male</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O 2. 3101293</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>384</td>
          <td>1</td>
          <td>1</td>
          <td>Holverson, Mrs. Alexander Oskar (Mary Aline Towner)</td>
          <td>female</td>
          <td>35.00</td>
          <td>1</td>
          <td>0</td>
          <td>113789</td>
          <td>52.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>385</td>
          <td>0</td>
          <td>3</td>
          <td>Plotcharsky, Mr. Vasil</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349227</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>386</td>
          <td>0</td>
          <td>2</td>
          <td>Davies, Mr. Charles Henry</td>
          <td>male</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>S.O.C. 14879</td>
          <td>73.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>387</td>
          <td>0</td>
          <td>3</td>
          <td>Goodwin, Master. Sidney Leonard</td>
          <td>male</td>
          <td>1.00</td>
          <td>5</td>
          <td>2</td>
          <td>CA 2144</td>
          <td>46.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>388</td>
          <td>1</td>
          <td>2</td>
          <td>Buss, Miss. Kate</td>
          <td>female</td>
          <td>36.00</td>
          <td>0</td>
          <td>0</td>
          <td>27849</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>389</td>
          <td>0</td>
          <td>3</td>
          <td>Sadlier, Mr. Matthew</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>367655</td>
          <td>7.7292</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>390</td>
          <td>1</td>
          <td>2</td>
          <td>Lehmann, Miss. Bertha</td>
          <td>female</td>
          <td>17.00</td>
          <td>0</td>
          <td>0</td>
          <td>SC 1748</td>
          <td>12.0000</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>391</td>
          <td>1</td>
          <td>1</td>
          <td>Carter, Mr. William Ernest</td>
          <td>male</td>
          <td>36.00</td>
          <td>1</td>
          <td>2</td>
          <td>113760</td>
          <td>120.0000</td>
          <td>B96 B98</td>
          <td>S</td>
        </tr>
        <tr>
          <td>392</td>
          <td>1</td>
          <td>3</td>
          <td>Jansson, Mr. Carl Olof</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>350034</td>
          <td>7.7958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>393</td>
          <td>0</td>
          <td>3</td>
          <td>Gustafsson, Mr. Johan Birger</td>
          <td>male</td>
          <td>28.00</td>
          <td>2</td>
          <td>0</td>
          <td>3101277</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>394</td>
          <td>1</td>
          <td>1</td>
          <td>Newell, Miss. Marjorie</td>
          <td>female</td>
          <td>23.00</td>
          <td>1</td>
          <td>0</td>
          <td>35273</td>
          <td>113.2750</td>
          <td>D36</td>
          <td>C</td>
        </tr>
        <tr>
          <td>395</td>
          <td>1</td>
          <td>3</td>
          <td>Sandstrom, Mrs. Hjalmar (Agnes Charlotta Bengtsson)</td>
          <td>female</td>
          <td>24.00</td>
          <td>0</td>
          <td>2</td>
          <td>PP 9549</td>
          <td>16.7000</td>
          <td>G6</td>
          <td>S</td>
        </tr>
        <tr>
          <td>396</td>
          <td>0</td>
          <td>3</td>
          <td>Johansson, Mr. Erik</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>350052</td>
          <td>7.7958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>397</td>
          <td>0</td>
          <td>3</td>
          <td>Olsson, Miss. Elina</td>
          <td>female</td>
          <td>31.00</td>
          <td>0</td>
          <td>0</td>
          <td>350407</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>398</td>
          <td>0</td>
          <td>2</td>
          <td>McKane, Mr. Peter David</td>
          <td>male</td>
          <td>46.00</td>
          <td>0</td>
          <td>0</td>
          <td>28403</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>399</td>
          <td>0</td>
          <td>2</td>
          <td>Pain, Dr. Alfred</td>
          <td>male</td>
          <td>23.00</td>
          <td>0</td>
          <td>0</td>
          <td>244278</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>400</td>
          <td>1</td>
          <td>2</td>
          <td>Trout, Mrs. William H (Jessie L)</td>
          <td>female</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>240929</td>
          <td>12.6500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>401</td>
          <td>1</td>
          <td>3</td>
          <td>Niskanen, Mr. Juha</td>
          <td>male</td>
          <td>39.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O 2. 3101289</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>402</td>
          <td>0</td>
          <td>3</td>
          <td>Adams, Mr. John</td>
          <td>male</td>
          <td>26.00</td>
          <td>0</td>
          <td>0</td>
          <td>341826</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>403</td>
          <td>0</td>
          <td>3</td>
          <td>Jussila, Miss. Mari Aina</td>
          <td>female</td>
          <td>21.00</td>
          <td>1</td>
          <td>0</td>
          <td>4137</td>
          <td>9.8250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>404</td>
          <td>0</td>
          <td>3</td>
          <td>Hakkarainen, Mr. Pekka Pietari</td>
          <td>male</td>
          <td>28.00</td>
          <td>1</td>
          <td>0</td>
          <td>STON/O2. 3101279</td>
          <td>15.8500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>405</td>
          <td>0</td>
          <td>3</td>
          <td>Oreskovic, Miss. Marija</td>
          <td>female</td>
          <td>20.00</td>
          <td>0</td>
          <td>0</td>
          <td>315096</td>
          <td>8.6625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>406</td>
          <td>0</td>
          <td>2</td>
          <td>Gale, Mr. Shadrach</td>
          <td>male</td>
          <td>34.00</td>
          <td>1</td>
          <td>0</td>
          <td>28664</td>
          <td>21.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>407</td>
          <td>0</td>
          <td>3</td>
          <td>Widegren, Mr. Carl/Charles Peter</td>
          <td>male</td>
          <td>51.00</td>
          <td>0</td>
          <td>0</td>
          <td>347064</td>
          <td>7.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>408</td>
          <td>1</td>
          <td>2</td>
          <td>Richards, Master. William Rowe</td>
          <td>male</td>
          <td>3.00</td>
          <td>1</td>
          <td>1</td>
          <td>29106</td>
          <td>18.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>409</td>
          <td>0</td>
          <td>3</td>
          <td>Birkeland, Mr. Hans Martin Monsen</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>312992</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>410</td>
          <td>0</td>
          <td>3</td>
          <td>Lefebre, Miss. Ida</td>
          <td>female</td>
          <td></td>
          <td>3</td>
          <td>1</td>
          <td>4133</td>
          <td>25.4667</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>411</td>
          <td>0</td>
          <td>3</td>
          <td>Sdycoff, Mr. Todor</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349222</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>412</td>
          <td>0</td>
          <td>3</td>
          <td>Hart, Mr. Henry</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>394140</td>
          <td>6.8583</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>413</td>
          <td>1</td>
          <td>1</td>
          <td>Minahan, Miss. Daisy E</td>
          <td>female</td>
          <td>33.00</td>
          <td>1</td>
          <td>0</td>
          <td>19928</td>
          <td>90.0000</td>
          <td>C78</td>
          <td>Q</td>
        </tr>
        <tr>
          <td>414</td>
          <td>0</td>
          <td>2</td>
          <td>Cunningham, Mr. Alfred Fleming</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>239853</td>
          <td>0.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>415</td>
          <td>1</td>
          <td>3</td>
          <td>Sundman, Mr. Johan Julian</td>
          <td>male</td>
          <td>44.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O 2. 3101269</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>416</td>
          <td>0</td>
          <td>3</td>
          <td>Meek, Mrs. Thomas (Annie Louise Rowley)</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>343095</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>417</td>
          <td>1</td>
          <td>2</td>
          <td>Drew, Mrs. James Vivian (Lulu Thorne Christian)</td>
          <td>female</td>
          <td>34.00</td>
          <td>1</td>
          <td>1</td>
          <td>28220</td>
          <td>32.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>418</td>
          <td>1</td>
          <td>2</td>
          <td>Silven, Miss. Lyyli Karoliina</td>
          <td>female</td>
          <td>18.00</td>
          <td>0</td>
          <td>2</td>
          <td>250652</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>419</td>
          <td>0</td>
          <td>2</td>
          <td>Matthews, Mr. William John</td>
          <td>male</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>28228</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>420</td>
          <td>0</td>
          <td>3</td>
          <td>Van Impe, Miss. Catharina</td>
          <td>female</td>
          <td>10.00</td>
          <td>0</td>
          <td>2</td>
          <td>345773</td>
          <td>24.1500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>421</td>
          <td>0</td>
          <td>3</td>
          <td>Gheorgheff, Mr. Stanio</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349254</td>
          <td>7.8958</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>422</td>
          <td>0</td>
          <td>3</td>
          <td>Charters, Mr. David</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/5. 13032</td>
          <td>7.7333</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>423</td>
          <td>0</td>
          <td>3</td>
          <td>Zimmerman, Mr. Leo</td>
          <td>male</td>
          <td>29.00</td>
          <td>0</td>
          <td>0</td>
          <td>315082</td>
          <td>7.8750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>424</td>
          <td>0</td>
          <td>3</td>
          <td>Danbom, Mrs. Ernst Gilbert (Anna Sigrid Maria Brogren)</td>
          <td>female</td>
          <td>28.00</td>
          <td>1</td>
          <td>1</td>
          <td>347080</td>
          <td>14.4000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>425</td>
          <td>0</td>
          <td>3</td>
          <td>Rosblom, Mr. Viktor Richard</td>
          <td>male</td>
          <td>18.00</td>
          <td>1</td>
          <td>1</td>
          <td>370129</td>
          <td>20.2125</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>426</td>
          <td>0</td>
          <td>3</td>
          <td>Wiseman, Mr. Phillippe</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>A/4. 34244</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>427</td>
          <td>1</td>
          <td>2</td>
          <td>Clarke, Mrs. Charles V (Ada Maria Winfield)</td>
          <td>female</td>
          <td>28.00</td>
          <td>1</td>
          <td>0</td>
          <td>2003</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>428</td>
          <td>1</td>
          <td>2</td>
          <td>Phillips, Miss. Kate Florence ("Mrs Kate Louise Phillips Marshall")</td>
          <td>female</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>250655</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>429</td>
          <td>0</td>
          <td>3</td>
          <td>Flynn, Mr. James</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>364851</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>430</td>
          <td>1</td>
          <td>3</td>
          <td>Pickard, Mr. Berk (Berk Trembisky)</td>
          <td>male</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/O.Q. 392078</td>
          <td>8.0500</td>
          <td>E10</td>
          <td>S</td>
        </tr>
        <tr>
          <td>431</td>
          <td>1</td>
          <td>1</td>
          <td>Bjornstrom-Steffansson, Mr. Mauritz Hakan</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>110564</td>
          <td>26.5500</td>
          <td>C52</td>
          <td>S</td>
        </tr>
        <tr>
          <td>432</td>
          <td>1</td>
          <td>3</td>
          <td>Thorneycroft, Mrs. Percival (Florence Kate White)</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>376564</td>
          <td>16.1000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>433</td>
          <td>1</td>
          <td>2</td>
          <td>Louch, Mrs. Charles Alexander (Alice Adelaide Slow)</td>
          <td>female</td>
          <td>42.00</td>
          <td>1</td>
          <td>0</td>
          <td>SC/AH 3085</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>434</td>
          <td>0</td>
          <td>3</td>
          <td>Kallio, Mr. Nikolai Erland</td>
          <td>male</td>
          <td>17.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O 2. 3101274</td>
          <td>7.1250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>435</td>
          <td>0</td>
          <td>1</td>
          <td>Silvey, Mr. William Baird</td>
          <td>male</td>
          <td>50.00</td>
          <td>1</td>
          <td>0</td>
          <td>13507</td>
          <td>55.9000</td>
          <td>E44</td>
          <td>S</td>
        </tr>
        <tr>
          <td>436</td>
          <td>1</td>
          <td>1</td>
          <td>Carter, Miss. Lucile Polk</td>
          <td>female</td>
          <td>14.00</td>
          <td>1</td>
          <td>2</td>
          <td>113760</td>
          <td>120.0000</td>
          <td>B96 B98</td>
          <td>S</td>
        </tr>
        <tr>
          <td>437</td>
          <td>0</td>
          <td>3</td>
          <td>Ford, Miss. Doolina Margaret "Daisy"</td>
          <td>female</td>
          <td>21.00</td>
          <td>2</td>
          <td>2</td>
          <td>W./C. 6608</td>
          <td>34.3750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>438</td>
          <td>1</td>
          <td>2</td>
          <td>Richards, Mrs. Sidney (Emily Hocking)</td>
          <td>female</td>
          <td>24.00</td>
          <td>2</td>
          <td>3</td>
          <td>29106</td>
          <td>18.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>439</td>
          <td>0</td>
          <td>1</td>
          <td>Fortune, Mr. Mark</td>
          <td>male</td>
          <td>64.00</td>
          <td>1</td>
          <td>4</td>
          <td>19950</td>
          <td>263.0000</td>
          <td>C23 C25 C27</td>
          <td>S</td>
        </tr>
        <tr>
          <td>440</td>
          <td>0</td>
          <td>2</td>
          <td>Kvillner, Mr. Johan Henrik Johannesson</td>
          <td>male</td>
          <td>31.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 18723</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>441</td>
          <td>1</td>
          <td>2</td>
          <td>Hart, Mrs. Benjamin (Esther Ada Bloomfield)</td>
          <td>female</td>
          <td>45.00</td>
          <td>1</td>
          <td>1</td>
          <td>F.C.C. 13529</td>
          <td>26.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>442</td>
          <td>0</td>
          <td>3</td>
          <td>Hampe, Mr. Leon</td>
          <td>male</td>
          <td>20.00</td>
          <td>0</td>
          <td>0</td>
          <td>345769</td>
          <td>9.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>443</td>
          <td>0</td>
          <td>3</td>
          <td>Petterson, Mr. Johan Emil</td>
          <td>male</td>
          <td>25.00</td>
          <td>1</td>
          <td>0</td>
          <td>347076</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>444</td>
          <td>1</td>
          <td>2</td>
          <td>Reynaldo, Ms. Encarnacion</td>
          <td>female</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>230434</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>445</td>
          <td>1</td>
          <td>3</td>
          <td>Johannesen-Bratthammer, Mr. Bernt</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>65306</td>
          <td>8.1125</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>446</td>
          <td>1</td>
          <td>1</td>
          <td>Dodge, Master. Washington</td>
          <td>male</td>
          <td>4.00</td>
          <td>0</td>
          <td>2</td>
          <td>33638</td>
          <td>81.8583</td>
          <td>A34</td>
          <td>S</td>
        </tr>
        <tr>
          <td>447</td>
          <td>1</td>
          <td>2</td>
          <td>Mellinger, Miss. Madeleine Violet</td>
          <td>female</td>
          <td>13.00</td>
          <td>0</td>
          <td>1</td>
          <td>250644</td>
          <td>19.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>448</td>
          <td>1</td>
          <td>1</td>
          <td>Seward, Mr. Frederic Kimber</td>
          <td>male</td>
          <td>34.00</td>
          <td>0</td>
          <td>0</td>
          <td>113794</td>
          <td>26.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>449</td>
          <td>1</td>
          <td>3</td>
          <td>Baclini, Miss. Marie Catherine</td>
          <td>female</td>
          <td>5.00</td>
          <td>2</td>
          <td>1</td>
          <td>2666</td>
          <td>19.2583</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>450</td>
          <td>1</td>
          <td>1</td>
          <td>Peuchen, Major. Arthur Godfrey</td>
          <td>male</td>
          <td>52.00</td>
          <td>0</td>
          <td>0</td>
          <td>113786</td>
          <td>30.5000</td>
          <td>C104</td>
          <td>S</td>
        </tr>
        <tr>
          <td>451</td>
          <td>0</td>
          <td>2</td>
          <td>West, Mr. Edwy Arthur</td>
          <td>male</td>
          <td>36.00</td>
          <td>1</td>
          <td>2</td>
          <td>C.A. 34651</td>
          <td>27.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>452</td>
          <td>0</td>
          <td>3</td>
          <td>Hagland, Mr. Ingvald Olai Olsen</td>
          <td>male</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>65303</td>
          <td>19.9667</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>453</td>
          <td>0</td>
          <td>1</td>
          <td>Foreman, Mr. Benjamin Laventall</td>
          <td>male</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>113051</td>
          <td>27.7500</td>
          <td>C111</td>
          <td>C</td>
        </tr>
        <tr>
          <td>454</td>
          <td>1</td>
          <td>1</td>
          <td>Goldenberg, Mr. Samuel L</td>
          <td>male</td>
          <td>49.00</td>
          <td>1</td>
          <td>0</td>
          <td>17453</td>
          <td>89.1042</td>
          <td>C92</td>
          <td>C</td>
        </tr>
        <tr>
          <td>455</td>
          <td>0</td>
          <td>3</td>
          <td>Peduzzi, Mr. Joseph</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>A/5 2817</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>456</td>
          <td>1</td>
          <td>3</td>
          <td>Jalsevac, Mr. Ivan</td>
          <td>male</td>
          <td>29.00</td>
          <td>0</td>
          <td>0</td>
          <td>349240</td>
          <td>7.8958</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>457</td>
          <td>0</td>
          <td>1</td>
          <td>Millet, Mr. Francis Davis</td>
          <td>male</td>
          <td>65.00</td>
          <td>0</td>
          <td>0</td>
          <td>13509</td>
          <td>26.5500</td>
          <td>E38</td>
          <td>S</td>
        </tr>
        <tr>
          <td>458</td>
          <td>1</td>
          <td>1</td>
          <td>Kenyon, Mrs. Frederick R (Marion)</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>17464</td>
          <td>51.8625</td>
          <td>D21</td>
          <td>S</td>
        </tr>
        <tr>
          <td>459</td>
          <td>1</td>
          <td>2</td>
          <td>Toomey, Miss. Ellen</td>
          <td>female</td>
          <td>50.00</td>
          <td>0</td>
          <td>0</td>
          <td>F.C.C. 13531</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>460</td>
          <td>0</td>
          <td>3</td>
          <td>O'Connor, Mr. Maurice</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>371060</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>461</td>
          <td>1</td>
          <td>1</td>
          <td>Anderson, Mr. Harry</td>
          <td>male</td>
          <td>48.00</td>
          <td>0</td>
          <td>0</td>
          <td>19952</td>
          <td>26.5500</td>
          <td>E12</td>
          <td>S</td>
        </tr>
        <tr>
          <td>462</td>
          <td>0</td>
          <td>3</td>
          <td>Morley, Mr. William</td>
          <td>male</td>
          <td>34.00</td>
          <td>0</td>
          <td>0</td>
          <td>364506</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>463</td>
          <td>0</td>
          <td>1</td>
          <td>Gee, Mr. Arthur H</td>
          <td>male</td>
          <td>47.00</td>
          <td>0</td>
          <td>0</td>
          <td>111320</td>
          <td>38.5000</td>
          <td>E63</td>
          <td>S</td>
        </tr>
        <tr>
          <td>464</td>
          <td>0</td>
          <td>2</td>
          <td>Milling, Mr. Jacob Christian</td>
          <td>male</td>
          <td>48.00</td>
          <td>0</td>
          <td>0</td>
          <td>234360</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>465</td>
          <td>0</td>
          <td>3</td>
          <td>Maisner, Mr. Simon</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>A/S 2816</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>466</td>
          <td>0</td>
          <td>3</td>
          <td>Goncalves, Mr. Manuel Estanslas</td>
          <td>male</td>
          <td>38.00</td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/O.Q. 3101306</td>
          <td>7.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>467</td>
          <td>0</td>
          <td>2</td>
          <td>Campbell, Mr. William</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>239853</td>
          <td>0.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>468</td>
          <td>0</td>
          <td>1</td>
          <td>Smart, Mr. John Montgomery</td>
          <td>male</td>
          <td>56.00</td>
          <td>0</td>
          <td>0</td>
          <td>113792</td>
          <td>26.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>469</td>
          <td>0</td>
          <td>3</td>
          <td>Scanlan, Mr. James</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>36209</td>
          <td>7.7250</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>470</td>
          <td>1</td>
          <td>3</td>
          <td>Baclini, Miss. Helene Barbara</td>
          <td>female</td>
          <td>0.75</td>
          <td>2</td>
          <td>1</td>
          <td>2666</td>
          <td>19.2583</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>471</td>
          <td>0</td>
          <td>3</td>
          <td>Keefe, Mr. Arthur</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>323592</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>472</td>
          <td>0</td>
          <td>3</td>
          <td>Cacic, Mr. Luka</td>
          <td>male</td>
          <td>38.00</td>
          <td>0</td>
          <td>0</td>
          <td>315089</td>
          <td>8.6625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>473</td>
          <td>1</td>
          <td>2</td>
          <td>West, Mrs. Edwy Arthur (Ada Mary Worth)</td>
          <td>female</td>
          <td>33.00</td>
          <td>1</td>
          <td>2</td>
          <td>C.A. 34651</td>
          <td>27.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>474</td>
          <td>1</td>
          <td>2</td>
          <td>Jerwan, Mrs. Amin S (Marie Marthe Thuillard)</td>
          <td>female</td>
          <td>23.00</td>
          <td>0</td>
          <td>0</td>
          <td>SC/AH Basle 541</td>
          <td>13.7917</td>
          <td>D</td>
          <td>C</td>
        </tr>
        <tr>
          <td>475</td>
          <td>0</td>
          <td>3</td>
          <td>Strandberg, Miss. Ida Sofia</td>
          <td>female</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>7553</td>
          <td>9.8375</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>476</td>
          <td>0</td>
          <td>1</td>
          <td>Clifford, Mr. George Quincy</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>110465</td>
          <td>52.0000</td>
          <td>A14</td>
          <td>S</td>
        </tr>
        <tr>
          <td>477</td>
          <td>0</td>
          <td>2</td>
          <td>Renouf, Mr. Peter Henry</td>
          <td>male</td>
          <td>34.00</td>
          <td>1</td>
          <td>0</td>
          <td>31027</td>
          <td>21.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>478</td>
          <td>0</td>
          <td>3</td>
          <td>Braund, Mr. Lewis Richard</td>
          <td>male</td>
          <td>29.00</td>
          <td>1</td>
          <td>0</td>
          <td>3460</td>
          <td>7.0458</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>479</td>
          <td>0</td>
          <td>3</td>
          <td>Karlsson, Mr. Nils August</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>350060</td>
          <td>7.5208</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>480</td>
          <td>1</td>
          <td>3</td>
          <td>Hirvonen, Miss. Hildur E</td>
          <td>female</td>
          <td>2.00</td>
          <td>0</td>
          <td>1</td>
          <td>3101298</td>
          <td>12.2875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>481</td>
          <td>0</td>
          <td>3</td>
          <td>Goodwin, Master. Harold Victor</td>
          <td>male</td>
          <td>9.00</td>
          <td>5</td>
          <td>2</td>
          <td>CA 2144</td>
          <td>46.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>482</td>
          <td>0</td>
          <td>2</td>
          <td>Frost, Mr. Anthony Wood "Archie"</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>239854</td>
          <td>0.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>483</td>
          <td>0</td>
          <td>3</td>
          <td>Rouse, Mr. Richard Henry</td>
          <td>male</td>
          <td>50.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/5 3594</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>484</td>
          <td>1</td>
          <td>3</td>
          <td>Turkula, Mrs. (Hedwig)</td>
          <td>female</td>
          <td>63.00</td>
          <td>0</td>
          <td>0</td>
          <td>4134</td>
          <td>9.5875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>485</td>
          <td>1</td>
          <td>1</td>
          <td>Bishop, Mr. Dickinson H</td>
          <td>male</td>
          <td>25.00</td>
          <td>1</td>
          <td>0</td>
          <td>11967</td>
          <td>91.0792</td>
          <td>B49</td>
          <td>C</td>
        </tr>
        <tr>
          <td>486</td>
          <td>0</td>
          <td>3</td>
          <td>Lefebre, Miss. Jeannie</td>
          <td>female</td>
          <td></td>
          <td>3</td>
          <td>1</td>
          <td>4133</td>
          <td>25.4667</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>487</td>
          <td>1</td>
          <td>1</td>
          <td>Hoyt, Mrs. Frederick Maxfield (Jane Anne Forby)</td>
          <td>female</td>
          <td>35.00</td>
          <td>1</td>
          <td>0</td>
          <td>19943</td>
          <td>90.0000</td>
          <td>C93</td>
          <td>S</td>
        </tr>
        <tr>
          <td>488</td>
          <td>0</td>
          <td>1</td>
          <td>Kent, Mr. Edward Austin</td>
          <td>male</td>
          <td>58.00</td>
          <td>0</td>
          <td>0</td>
          <td>11771</td>
          <td>29.7000</td>
          <td>B37</td>
          <td>C</td>
        </tr>
        <tr>
          <td>489</td>
          <td>0</td>
          <td>3</td>
          <td>Somerton, Mr. Francis William</td>
          <td>male</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>A.5. 18509</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>490</td>
          <td>1</td>
          <td>3</td>
          <td>Coutts, Master. Eden Leslie "Neville"</td>
          <td>male</td>
          <td>9.00</td>
          <td>1</td>
          <td>1</td>
          <td>C.A. 37671</td>
          <td>15.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>491</td>
          <td>0</td>
          <td>3</td>
          <td>Hagland, Mr. Konrad Mathias Reiersen</td>
          <td>male</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>65304</td>
          <td>19.9667</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>492</td>
          <td>0</td>
          <td>3</td>
          <td>Windelov, Mr. Einar</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/OQ 3101317</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>493</td>
          <td>0</td>
          <td>1</td>
          <td>Molson, Mr. Harry Markland</td>
          <td>male</td>
          <td>55.00</td>
          <td>0</td>
          <td>0</td>
          <td>113787</td>
          <td>30.5000</td>
          <td>C30</td>
          <td>S</td>
        </tr>
        <tr>
          <td>494</td>
          <td>0</td>
          <td>1</td>
          <td>Artagaveytia, Mr. Ramon</td>
          <td>male</td>
          <td>71.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17609</td>
          <td>49.5042</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>495</td>
          <td>0</td>
          <td>3</td>
          <td>Stanley, Mr. Edward Roland</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/4 45380</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>496</td>
          <td>0</td>
          <td>3</td>
          <td>Yousseff, Mr. Gerious</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2627</td>
          <td>14.4583</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>497</td>
          <td>1</td>
          <td>1</td>
          <td>Eustis, Miss. Elizabeth Mussey</td>
          <td>female</td>
          <td>54.00</td>
          <td>1</td>
          <td>0</td>
          <td>36947</td>
          <td>78.2667</td>
          <td>D20</td>
          <td>C</td>
        </tr>
        <tr>
          <td>498</td>
          <td>0</td>
          <td>3</td>
          <td>Shellard, Mr. Frederick William</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 6212</td>
          <td>15.1000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>499</td>
          <td>0</td>
          <td>1</td>
          <td>Allison, Mrs. Hudson J C (Bessie Waldo Daniels)</td>
          <td>female</td>
          <td>25.00</td>
          <td>1</td>
          <td>2</td>
          <td>113781</td>
          <td>151.5500</td>
          <td>C22 C26</td>
          <td>S</td>
        </tr>
        <tr>
          <td>500</td>
          <td>0</td>
          <td>3</td>
          <td>Svensson, Mr. Olof</td>
          <td>male</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>350035</td>
          <td>7.7958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>501</td>
          <td>0</td>
          <td>3</td>
          <td>Calic, Mr. Petar</td>
          <td>male</td>
          <td>17.00</td>
          <td>0</td>
          <td>0</td>
          <td>315086</td>
          <td>8.6625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>502</td>
          <td>0</td>
          <td>3</td>
          <td>Canavan, Miss. Mary</td>
          <td>female</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>364846</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>503</td>
          <td>0</td>
          <td>3</td>
          <td>O'Sullivan, Miss. Bridget Mary</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>330909</td>
          <td>7.6292</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>504</td>
          <td>0</td>
          <td>3</td>
          <td>Laitinen, Miss. Kristina Sofia</td>
          <td>female</td>
          <td>37.00</td>
          <td>0</td>
          <td>0</td>
          <td>4135</td>
          <td>9.5875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>505</td>
          <td>1</td>
          <td>1</td>
          <td>Maioni, Miss. Roberta</td>
          <td>female</td>
          <td>16.00</td>
          <td>0</td>
          <td>0</td>
          <td>110152</td>
          <td>86.5000</td>
          <td>B79</td>
          <td>S</td>
        </tr>
        <tr>
          <td>506</td>
          <td>0</td>
          <td>1</td>
          <td>Penasco y Castellana, Mr. Victor de Satode</td>
          <td>male</td>
          <td>18.00</td>
          <td>1</td>
          <td>0</td>
          <td>PC 17758</td>
          <td>108.9000</td>
          <td>C65</td>
          <td>C</td>
        </tr>
        <tr>
          <td>507</td>
          <td>1</td>
          <td>2</td>
          <td>Quick, Mrs. Frederick Charles (Jane Richards)</td>
          <td>female</td>
          <td>33.00</td>
          <td>0</td>
          <td>2</td>
          <td>26360</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>508</td>
          <td>1</td>
          <td>1</td>
          <td>Bradley, Mr. George ("George Arthur Brayton")</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>111427</td>
          <td>26.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>509</td>
          <td>0</td>
          <td>3</td>
          <td>Olsen, Mr. Henry Margido</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>C 4001</td>
          <td>22.5250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>510</td>
          <td>1</td>
          <td>3</td>
          <td>Lang, Mr. Fang</td>
          <td>male</td>
          <td>26.00</td>
          <td>0</td>
          <td>0</td>
          <td>1601</td>
          <td>56.4958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>511</td>
          <td>1</td>
          <td>3</td>
          <td>Daly, Mr. Eugene Patrick</td>
          <td>male</td>
          <td>29.00</td>
          <td>0</td>
          <td>0</td>
          <td>382651</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>512</td>
          <td>0</td>
          <td>3</td>
          <td>Webber, Mr. James</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/OQ 3101316</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>513</td>
          <td>1</td>
          <td>1</td>
          <td>McGough, Mr. James Robert</td>
          <td>male</td>
          <td>36.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17473</td>
          <td>26.2875</td>
          <td>E25</td>
          <td>S</td>
        </tr>
        <tr>
          <td>514</td>
          <td>1</td>
          <td>1</td>
          <td>Rothschild, Mrs. Martin (Elizabeth L. Barrett)</td>
          <td>female</td>
          <td>54.00</td>
          <td>1</td>
          <td>0</td>
          <td>PC 17603</td>
          <td>59.4000</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>515</td>
          <td>0</td>
          <td>3</td>
          <td>Coleff, Mr. Satio</td>
          <td>male</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>349209</td>
          <td>7.4958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>516</td>
          <td>0</td>
          <td>1</td>
          <td>Walker, Mr. William Anderson</td>
          <td>male</td>
          <td>47.00</td>
          <td>0</td>
          <td>0</td>
          <td>36967</td>
          <td>34.0208</td>
          <td>D46</td>
          <td>S</td>
        </tr>
        <tr>
          <td>517</td>
          <td>1</td>
          <td>2</td>
          <td>Lemore, Mrs. (Amelia Milley)</td>
          <td>female</td>
          <td>34.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 34260</td>
          <td>10.5000</td>
          <td>F33</td>
          <td>S</td>
        </tr>
        <tr>
          <td>518</td>
          <td>0</td>
          <td>3</td>
          <td>Ryan, Mr. Patrick</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>371110</td>
          <td>24.1500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>519</td>
          <td>1</td>
          <td>2</td>
          <td>Angle, Mrs. William A (Florence "Mary" Agnes Hughes)</td>
          <td>female</td>
          <td>36.00</td>
          <td>1</td>
          <td>0</td>
          <td>226875</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>520</td>
          <td>0</td>
          <td>3</td>
          <td>Pavlovic, Mr. Stefo</td>
          <td>male</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>349242</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>521</td>
          <td>1</td>
          <td>1</td>
          <td>Perreault, Miss. Anne</td>
          <td>female</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>12749</td>
          <td>93.5000</td>
          <td>B73</td>
          <td>S</td>
        </tr>
        <tr>
          <td>522</td>
          <td>0</td>
          <td>3</td>
          <td>Vovk, Mr. Janko</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>349252</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>523</td>
          <td>0</td>
          <td>3</td>
          <td>Lahoud, Mr. Sarkis</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2624</td>
          <td>7.2250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>524</td>
          <td>1</td>
          <td>1</td>
          <td>Hippach, Mrs. Louis Albert (Ida Sophia Fischer)</td>
          <td>female</td>
          <td>44.00</td>
          <td>0</td>
          <td>1</td>
          <td>111361</td>
          <td>57.9792</td>
          <td>B18</td>
          <td>C</td>
        </tr>
        <tr>
          <td>525</td>
          <td>0</td>
          <td>3</td>
          <td>Kassem, Mr. Fared</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2700</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>526</td>
          <td>0</td>
          <td>3</td>
          <td>Farrell, Mr. James</td>
          <td>male</td>
          <td>40.50</td>
          <td>0</td>
          <td>0</td>
          <td>367232</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>527</td>
          <td>1</td>
          <td>2</td>
          <td>Ridsdale, Miss. Lucy</td>
          <td>female</td>
          <td>50.00</td>
          <td>0</td>
          <td>0</td>
          <td>W./C. 14258</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>528</td>
          <td>0</td>
          <td>1</td>
          <td>Farthing, Mr. John</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>PC 17483</td>
          <td>221.7792</td>
          <td>C95</td>
          <td>S</td>
        </tr>
        <tr>
          <td>529</td>
          <td>0</td>
          <td>3</td>
          <td>Salonen, Mr. Johan Werner</td>
          <td>male</td>
          <td>39.00</td>
          <td>0</td>
          <td>0</td>
          <td>3101296</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>530</td>
          <td>0</td>
          <td>2</td>
          <td>Hocking, Mr. Richard George</td>
          <td>male</td>
          <td>23.00</td>
          <td>2</td>
          <td>1</td>
          <td>29104</td>
          <td>11.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>531</td>
          <td>1</td>
          <td>2</td>
          <td>Quick, Miss. Phyllis May</td>
          <td>female</td>
          <td>2.00</td>
          <td>1</td>
          <td>1</td>
          <td>26360</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>532</td>
          <td>0</td>
          <td>3</td>
          <td>Toufik, Mr. Nakli</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2641</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>533</td>
          <td>0</td>
          <td>3</td>
          <td>Elias, Mr. Joseph Jr</td>
          <td>male</td>
          <td>17.00</td>
          <td>1</td>
          <td>1</td>
          <td>2690</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>534</td>
          <td>1</td>
          <td>3</td>
          <td>Peter, Mrs. Catherine (Catherine Rizk)</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>2</td>
          <td>2668</td>
          <td>22.3583</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>535</td>
          <td>0</td>
          <td>3</td>
          <td>Cacic, Miss. Marija</td>
          <td>female</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>315084</td>
          <td>8.6625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>536</td>
          <td>1</td>
          <td>2</td>
          <td>Hart, Miss. Eva Miriam</td>
          <td>female</td>
          <td>7.00</td>
          <td>0</td>
          <td>2</td>
          <td>F.C.C. 13529</td>
          <td>26.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>537</td>
          <td>0</td>
          <td>1</td>
          <td>Butt, Major. Archibald Willingham</td>
          <td>male</td>
          <td>45.00</td>
          <td>0</td>
          <td>0</td>
          <td>113050</td>
          <td>26.5500</td>
          <td>B38</td>
          <td>S</td>
        </tr>
        <tr>
          <td>538</td>
          <td>1</td>
          <td>1</td>
          <td>LeRoy, Miss. Bertha</td>
          <td>female</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17761</td>
          <td>106.4250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>539</td>
          <td>0</td>
          <td>3</td>
          <td>Risien, Mr. Samuel Beard</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>364498</td>
          <td>14.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>540</td>
          <td>1</td>
          <td>1</td>
          <td>Frolicher, Miss. Hedwig Margaritha</td>
          <td>female</td>
          <td>22.00</td>
          <td>0</td>
          <td>2</td>
          <td>13568</td>
          <td>49.5000</td>
          <td>B39</td>
          <td>C</td>
        </tr>
        <tr>
          <td>541</td>
          <td>1</td>
          <td>1</td>
          <td>Crosby, Miss. Harriet R</td>
          <td>female</td>
          <td>36.00</td>
          <td>0</td>
          <td>2</td>
          <td>WE/P 5735</td>
          <td>71.0000</td>
          <td>B22</td>
          <td>S</td>
        </tr>
        <tr>
          <td>542</td>
          <td>0</td>
          <td>3</td>
          <td>Andersson, Miss. Ingeborg Constanzia</td>
          <td>female</td>
          <td>9.00</td>
          <td>4</td>
          <td>2</td>
          <td>347082</td>
          <td>31.2750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>543</td>
          <td>0</td>
          <td>3</td>
          <td>Andersson, Miss. Sigrid Elisabeth</td>
          <td>female</td>
          <td>11.00</td>
          <td>4</td>
          <td>2</td>
          <td>347082</td>
          <td>31.2750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>544</td>
          <td>1</td>
          <td>2</td>
          <td>Beane, Mr. Edward</td>
          <td>male</td>
          <td>32.00</td>
          <td>1</td>
          <td>0</td>
          <td>2908</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>545</td>
          <td>0</td>
          <td>1</td>
          <td>Douglas, Mr. Walter Donald</td>
          <td>male</td>
          <td>50.00</td>
          <td>1</td>
          <td>0</td>
          <td>PC 17761</td>
          <td>106.4250</td>
          <td>C86</td>
          <td>C</td>
        </tr>
        <tr>
          <td>546</td>
          <td>0</td>
          <td>1</td>
          <td>Nicholson, Mr. Arthur Ernest</td>
          <td>male</td>
          <td>64.00</td>
          <td>0</td>
          <td>0</td>
          <td>693</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>547</td>
          <td>1</td>
          <td>2</td>
          <td>Beane, Mrs. Edward (Ethel Clarke)</td>
          <td>female</td>
          <td>19.00</td>
          <td>1</td>
          <td>0</td>
          <td>2908</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>548</td>
          <td>1</td>
          <td>2</td>
          <td>Padro y Manent, Mr. Julian</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>SC/PARIS 2146</td>
          <td>13.8625</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>549</td>
          <td>0</td>
          <td>3</td>
          <td>Goldsmith, Mr. Frank John</td>
          <td>male</td>
          <td>33.00</td>
          <td>1</td>
          <td>1</td>
          <td>363291</td>
          <td>20.5250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>550</td>
          <td>1</td>
          <td>2</td>
          <td>Davies, Master. John Morgan Jr</td>
          <td>male</td>
          <td>8.00</td>
          <td>1</td>
          <td>1</td>
          <td>C.A. 33112</td>
          <td>36.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>551</td>
          <td>1</td>
          <td>1</td>
          <td>Thayer, Mr. John Borland Jr</td>
          <td>male</td>
          <td>17.00</td>
          <td>0</td>
          <td>2</td>
          <td>17421</td>
          <td>110.8833</td>
          <td>C70</td>
          <td>C</td>
        </tr>
        <tr>
          <td>552</td>
          <td>0</td>
          <td>2</td>
          <td>Sharp, Mr. Percival James R</td>
          <td>male</td>
          <td>27.00</td>
          <td>0</td>
          <td>0</td>
          <td>244358</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>553</td>
          <td>0</td>
          <td>3</td>
          <td>O'Brien, Mr. Timothy</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>330979</td>
          <td>7.8292</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>554</td>
          <td>1</td>
          <td>3</td>
          <td>Leeni, Mr. Fahim ("Philip Zenni")</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>2620</td>
          <td>7.2250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>555</td>
          <td>1</td>
          <td>3</td>
          <td>Ohman, Miss. Velin</td>
          <td>female</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>347085</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>556</td>
          <td>0</td>
          <td>1</td>
          <td>Wright, Mr. George</td>
          <td>male</td>
          <td>62.00</td>
          <td>0</td>
          <td>0</td>
          <td>113807</td>
          <td>26.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>557</td>
          <td>1</td>
          <td>1</td>
          <td>Duff Gordon, Lady. (Lucille Christiana Sutherland) ("Mrs Morgan")</td>
          <td>female</td>
          <td>48.00</td>
          <td>1</td>
          <td>0</td>
          <td>11755</td>
          <td>39.6000</td>
          <td>A16</td>
          <td>C</td>
        </tr>
        <tr>
          <td>558</td>
          <td>0</td>
          <td>1</td>
          <td>Robbins, Mr. Victor</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>PC 17757</td>
          <td>227.5250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>559</td>
          <td>1</td>
          <td>1</td>
          <td>Taussig, Mrs. Emil (Tillie Mandelbaum)</td>
          <td>female</td>
          <td>39.00</td>
          <td>1</td>
          <td>1</td>
          <td>110413</td>
          <td>79.6500</td>
          <td>E67</td>
          <td>S</td>
        </tr>
        <tr>
          <td>560</td>
          <td>1</td>
          <td>3</td>
          <td>de Messemaeker, Mrs. Guillaume Joseph (Emma)</td>
          <td>female</td>
          <td>36.00</td>
          <td>1</td>
          <td>0</td>
          <td>345572</td>
          <td>17.4000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>561</td>
          <td>0</td>
          <td>3</td>
          <td>Morrow, Mr. Thomas Rowan</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>372622</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>562</td>
          <td>0</td>
          <td>3</td>
          <td>Sivic, Mr. Husein</td>
          <td>male</td>
          <td>40.00</td>
          <td>0</td>
          <td>0</td>
          <td>349251</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>563</td>
          <td>0</td>
          <td>2</td>
          <td>Norman, Mr. Robert Douglas</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>218629</td>
          <td>13.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>564</td>
          <td>0</td>
          <td>3</td>
          <td>Simmons, Mr. John</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/OQ 392082</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>565</td>
          <td>0</td>
          <td>3</td>
          <td>Meanwell, Miss. (Marion Ogden)</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/O.Q. 392087</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>566</td>
          <td>0</td>
          <td>3</td>
          <td>Davies, Mr. Alfred J</td>
          <td>male</td>
          <td>24.00</td>
          <td>2</td>
          <td>0</td>
          <td>A/4 48871</td>
          <td>24.1500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>567</td>
          <td>0</td>
          <td>3</td>
          <td>Stoytcheff, Mr. Ilia</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>349205</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>568</td>
          <td>0</td>
          <td>3</td>
          <td>Palsson, Mrs. Nils (Alma Cornelia Berglund)</td>
          <td>female</td>
          <td>29.00</td>
          <td>0</td>
          <td>4</td>
          <td>349909</td>
          <td>21.0750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>569</td>
          <td>0</td>
          <td>3</td>
          <td>Doharr, Mr. Tannous</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2686</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>570</td>
          <td>1</td>
          <td>3</td>
          <td>Jonsson, Mr. Carl</td>
          <td>male</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>350417</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>571</td>
          <td>1</td>
          <td>2</td>
          <td>Harris, Mr. George</td>
          <td>male</td>
          <td>62.00</td>
          <td>0</td>
          <td>0</td>
          <td>S.W./PP 752</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>572</td>
          <td>1</td>
          <td>1</td>
          <td>Appleton, Mrs. Edward Dale (Charlotte Lamson)</td>
          <td>female</td>
          <td>53.00</td>
          <td>2</td>
          <td>0</td>
          <td>11769</td>
          <td>51.4792</td>
          <td>C101</td>
          <td>S</td>
        </tr>
        <tr>
          <td>573</td>
          <td>1</td>
          <td>1</td>
          <td>Flynn, Mr. John Irwin ("Irving")</td>
          <td>male</td>
          <td>36.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17474</td>
          <td>26.3875</td>
          <td>E25</td>
          <td>S</td>
        </tr>
        <tr>
          <td>574</td>
          <td>1</td>
          <td>3</td>
          <td>Kelly, Miss. Mary</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>14312</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>575</td>
          <td>0</td>
          <td>3</td>
          <td>Rush, Mr. Alfred George John</td>
          <td>male</td>
          <td>16.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/4. 20589</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>576</td>
          <td>0</td>
          <td>3</td>
          <td>Patchett, Mr. George</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>358585</td>
          <td>14.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>577</td>
          <td>1</td>
          <td>2</td>
          <td>Garside, Miss. Ethel</td>
          <td>female</td>
          <td>34.00</td>
          <td>0</td>
          <td>0</td>
          <td>243880</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>578</td>
          <td>1</td>
          <td>1</td>
          <td>Silvey, Mrs. William Baird (Alice Munger)</td>
          <td>female</td>
          <td>39.00</td>
          <td>1</td>
          <td>0</td>
          <td>13507</td>
          <td>55.9000</td>
          <td>E44</td>
          <td>S</td>
        </tr>
        <tr>
          <td>579</td>
          <td>0</td>
          <td>3</td>
          <td>Caram, Mrs. Joseph (Maria Elias)</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>2689</td>
          <td>14.4583</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>580</td>
          <td>1</td>
          <td>3</td>
          <td>Jussila, Mr. Eiriik</td>
          <td>male</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O 2. 3101286</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>581</td>
          <td>1</td>
          <td>2</td>
          <td>Christy, Miss. Julie Rachel</td>
          <td>female</td>
          <td>25.00</td>
          <td>1</td>
          <td>1</td>
          <td>237789</td>
          <td>30.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>582</td>
          <td>1</td>
          <td>1</td>
          <td>Thayer, Mrs. John Borland (Marian Longstreth Morris)</td>
          <td>female</td>
          <td>39.00</td>
          <td>1</td>
          <td>1</td>
          <td>17421</td>
          <td>110.8833</td>
          <td>C68</td>
          <td>C</td>
        </tr>
        <tr>
          <td>583</td>
          <td>0</td>
          <td>2</td>
          <td>Downton, Mr. William James</td>
          <td>male</td>
          <td>54.00</td>
          <td>0</td>
          <td>0</td>
          <td>28403</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>584</td>
          <td>0</td>
          <td>1</td>
          <td>Ross, Mr. John Hugo</td>
          <td>male</td>
          <td>36.00</td>
          <td>0</td>
          <td>0</td>
          <td>13049</td>
          <td>40.1250</td>
          <td>A10</td>
          <td>C</td>
        </tr>
        <tr>
          <td>585</td>
          <td>0</td>
          <td>3</td>
          <td>Paulner, Mr. Uscher</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>3411</td>
          <td>8.7125</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>586</td>
          <td>1</td>
          <td>1</td>
          <td>Taussig, Miss. Ruth</td>
          <td>female</td>
          <td>18.00</td>
          <td>0</td>
          <td>2</td>
          <td>110413</td>
          <td>79.6500</td>
          <td>E68</td>
          <td>S</td>
        </tr>
        <tr>
          <td>587</td>
          <td>0</td>
          <td>2</td>
          <td>Jarvis, Mr. John Denzil</td>
          <td>male</td>
          <td>47.00</td>
          <td>0</td>
          <td>0</td>
          <td>237565</td>
          <td>15.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>588</td>
          <td>1</td>
          <td>1</td>
          <td>Frolicher-Stehli, Mr. Maxmillian</td>
          <td>male</td>
          <td>60.00</td>
          <td>1</td>
          <td>1</td>
          <td>13567</td>
          <td>79.2000</td>
          <td>B41</td>
          <td>C</td>
        </tr>
        <tr>
          <td>589</td>
          <td>0</td>
          <td>3</td>
          <td>Gilinski, Mr. Eliezer</td>
          <td>male</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>14973</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>590</td>
          <td>0</td>
          <td>3</td>
          <td>Murdlin, Mr. Joseph</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>A./5. 3235</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>591</td>
          <td>0</td>
          <td>3</td>
          <td>Rintamaki, Mr. Matti</td>
          <td>male</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O 2. 3101273</td>
          <td>7.1250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>592</td>
          <td>1</td>
          <td>1</td>
          <td>Stephenson, Mrs. Walter Bertram (Martha Eustis)</td>
          <td>female</td>
          <td>52.00</td>
          <td>1</td>
          <td>0</td>
          <td>36947</td>
          <td>78.2667</td>
          <td>D20</td>
          <td>C</td>
        </tr>
        <tr>
          <td>593</td>
          <td>0</td>
          <td>3</td>
          <td>Elsbury, Mr. William James</td>
          <td>male</td>
          <td>47.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/5 3902</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>594</td>
          <td>0</td>
          <td>3</td>
          <td>Bourke, Miss. Mary</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>2</td>
          <td>364848</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>595</td>
          <td>0</td>
          <td>2</td>
          <td>Chapman, Mr. John Henry</td>
          <td>male</td>
          <td>37.00</td>
          <td>1</td>
          <td>0</td>
          <td>SC/AH 29037</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>596</td>
          <td>0</td>
          <td>3</td>
          <td>Van Impe, Mr. Jean Baptiste</td>
          <td>male</td>
          <td>36.00</td>
          <td>1</td>
          <td>1</td>
          <td>345773</td>
          <td>24.1500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>597</td>
          <td>1</td>
          <td>2</td>
          <td>Leitch, Miss. Jessie Wills</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>248727</td>
          <td>33.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>598</td>
          <td>0</td>
          <td>3</td>
          <td>Johnson, Mr. Alfred</td>
          <td>male</td>
          <td>49.00</td>
          <td>0</td>
          <td>0</td>
          <td>LINE</td>
          <td>0.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>599</td>
          <td>0</td>
          <td>3</td>
          <td>Boulos, Mr. Hanna</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2664</td>
          <td>7.2250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>600</td>
          <td>1</td>
          <td>1</td>
          <td>Duff Gordon, Sir. Cosmo Edmund ("Mr Morgan")</td>
          <td>male</td>
          <td>49.00</td>
          <td>1</td>
          <td>0</td>
          <td>PC 17485</td>
          <td>56.9292</td>
          <td>A20</td>
          <td>C</td>
        </tr>
        <tr>
          <td>601</td>
          <td>1</td>
          <td>2</td>
          <td>Jacobsohn, Mrs. Sidney Samuel (Amy Frances Christy)</td>
          <td>female</td>
          <td>24.00</td>
          <td>2</td>
          <td>1</td>
          <td>243847</td>
          <td>27.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>602</td>
          <td>0</td>
          <td>3</td>
          <td>Slabenoff, Mr. Petco</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349214</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>603</td>
          <td>0</td>
          <td>1</td>
          <td>Harrington, Mr. Charles H</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>113796</td>
          <td>42.4000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>604</td>
          <td>0</td>
          <td>3</td>
          <td>Torber, Mr. Ernst William</td>
          <td>male</td>
          <td>44.00</td>
          <td>0</td>
          <td>0</td>
          <td>364511</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>605</td>
          <td>1</td>
          <td>1</td>
          <td>Homer, Mr. Harry ("Mr E Haven")</td>
          <td>male</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>111426</td>
          <td>26.5500</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>606</td>
          <td>0</td>
          <td>3</td>
          <td>Lindell, Mr. Edvard Bengtsson</td>
          <td>male</td>
          <td>36.00</td>
          <td>1</td>
          <td>0</td>
          <td>349910</td>
          <td>15.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>607</td>
          <td>0</td>
          <td>3</td>
          <td>Karaic, Mr. Milan</td>
          <td>male</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>349246</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>608</td>
          <td>1</td>
          <td>1</td>
          <td>Daniel, Mr. Robert Williams</td>
          <td>male</td>
          <td>27.00</td>
          <td>0</td>
          <td>0</td>
          <td>113804</td>
          <td>30.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>609</td>
          <td>1</td>
          <td>2</td>
          <td>Laroche, Mrs. Joseph (Juliette Marie Louise Lafargue)</td>
          <td>female</td>
          <td>22.00</td>
          <td>1</td>
          <td>2</td>
          <td>SC/Paris 2123</td>
          <td>41.5792</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>610</td>
          <td>1</td>
          <td>1</td>
          <td>Shutes, Miss. Elizabeth W</td>
          <td>female</td>
          <td>40.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17582</td>
          <td>153.4625</td>
          <td>C125</td>
          <td>S</td>
        </tr>
        <tr>
          <td>611</td>
          <td>0</td>
          <td>3</td>
          <td>Andersson, Mrs. Anders Johan (Alfrida Konstantia Brogren)</td>
          <td>female</td>
          <td>39.00</td>
          <td>1</td>
          <td>5</td>
          <td>347082</td>
          <td>31.2750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>612</td>
          <td>0</td>
          <td>3</td>
          <td>Jardin, Mr. Jose Neto</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/O.Q. 3101305</td>
          <td>7.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>613</td>
          <td>1</td>
          <td>3</td>
          <td>Murphy, Miss. Margaret Jane</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>367230</td>
          <td>15.5000</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>614</td>
          <td>0</td>
          <td>3</td>
          <td>Horgan, Mr. John</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>370377</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>615</td>
          <td>0</td>
          <td>3</td>
          <td>Brocklebank, Mr. William Alfred</td>
          <td>male</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>364512</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>616</td>
          <td>1</td>
          <td>2</td>
          <td>Herman, Miss. Alice</td>
          <td>female</td>
          <td>24.00</td>
          <td>1</td>
          <td>2</td>
          <td>220845</td>
          <td>65.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>617</td>
          <td>0</td>
          <td>3</td>
          <td>Danbom, Mr. Ernst Gilbert</td>
          <td>male</td>
          <td>34.00</td>
          <td>1</td>
          <td>1</td>
          <td>347080</td>
          <td>14.4000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>618</td>
          <td>0</td>
          <td>3</td>
          <td>Lobb, Mrs. William Arthur (Cordelia K Stanlick)</td>
          <td>female</td>
          <td>26.00</td>
          <td>1</td>
          <td>0</td>
          <td>A/5. 3336</td>
          <td>16.1000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>619</td>
          <td>1</td>
          <td>2</td>
          <td>Becker, Miss. Marion Louise</td>
          <td>female</td>
          <td>4.00</td>
          <td>2</td>
          <td>1</td>
          <td>230136</td>
          <td>39.0000</td>
          <td>F4</td>
          <td>S</td>
        </tr>
        <tr>
          <td>620</td>
          <td>0</td>
          <td>2</td>
          <td>Gavey, Mr. Lawrence</td>
          <td>male</td>
          <td>26.00</td>
          <td>0</td>
          <td>0</td>
          <td>31028</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>621</td>
          <td>0</td>
          <td>3</td>
          <td>Yasbeck, Mr. Antoni</td>
          <td>male</td>
          <td>27.00</td>
          <td>1</td>
          <td>0</td>
          <td>2659</td>
          <td>14.4542</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>622</td>
          <td>1</td>
          <td>1</td>
          <td>Kimball, Mr. Edwin Nelson Jr</td>
          <td>male</td>
          <td>42.00</td>
          <td>1</td>
          <td>0</td>
          <td>11753</td>
          <td>52.5542</td>
          <td>D19</td>
          <td>S</td>
        </tr>
        <tr>
          <td>623</td>
          <td>1</td>
          <td>3</td>
          <td>Nakid, Mr. Sahid</td>
          <td>male</td>
          <td>20.00</td>
          <td>1</td>
          <td>1</td>
          <td>2653</td>
          <td>15.7417</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>624</td>
          <td>0</td>
          <td>3</td>
          <td>Hansen, Mr. Henry Damsgaard</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>350029</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>625</td>
          <td>0</td>
          <td>3</td>
          <td>Bowen, Mr. David John "Dai"</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>54636</td>
          <td>16.1000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>626</td>
          <td>0</td>
          <td>1</td>
          <td>Sutton, Mr. Frederick</td>
          <td>male</td>
          <td>61.00</td>
          <td>0</td>
          <td>0</td>
          <td>36963</td>
          <td>32.3208</td>
          <td>D50</td>
          <td>S</td>
        </tr>
        <tr>
          <td>627</td>
          <td>0</td>
          <td>2</td>
          <td>Kirkland, Rev. Charles Leonard</td>
          <td>male</td>
          <td>57.00</td>
          <td>0</td>
          <td>0</td>
          <td>219533</td>
          <td>12.3500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>628</td>
          <td>1</td>
          <td>1</td>
          <td>Longley, Miss. Gretchen Fiske</td>
          <td>female</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>13502</td>
          <td>77.9583</td>
          <td>D9</td>
          <td>S</td>
        </tr>
        <tr>
          <td>629</td>
          <td>0</td>
          <td>3</td>
          <td>Bostandyeff, Mr. Guentcho</td>
          <td>male</td>
          <td>26.00</td>
          <td>0</td>
          <td>0</td>
          <td>349224</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>630</td>
          <td>0</td>
          <td>3</td>
          <td>O'Connell, Mr. Patrick D</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>334912</td>
          <td>7.7333</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>631</td>
          <td>1</td>
          <td>1</td>
          <td>Barkworth, Mr. Algernon Henry Wilson</td>
          <td>male</td>
          <td>80.00</td>
          <td>0</td>
          <td>0</td>
          <td>27042</td>
          <td>30.0000</td>
          <td>A23</td>
          <td>S</td>
        </tr>
        <tr>
          <td>632</td>
          <td>0</td>
          <td>3</td>
          <td>Lundahl, Mr. Johan Svensson</td>
          <td>male</td>
          <td>51.00</td>
          <td>0</td>
          <td>0</td>
          <td>347743</td>
          <td>7.0542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>633</td>
          <td>1</td>
          <td>1</td>
          <td>Stahelin-Maeglin, Dr. Max</td>
          <td>male</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>13214</td>
          <td>30.5000</td>
          <td>B50</td>
          <td>C</td>
        </tr>
        <tr>
          <td>634</td>
          <td>0</td>
          <td>1</td>
          <td>Parr, Mr. William Henry Marsh</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>112052</td>
          <td>0.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>635</td>
          <td>0</td>
          <td>3</td>
          <td>Skoog, Miss. Mabel</td>
          <td>female</td>
          <td>9.00</td>
          <td>3</td>
          <td>2</td>
          <td>347088</td>
          <td>27.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>636</td>
          <td>1</td>
          <td>2</td>
          <td>Davis, Miss. Mary</td>
          <td>female</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>237668</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>637</td>
          <td>0</td>
          <td>3</td>
          <td>Leinonen, Mr. Antti Gustaf</td>
          <td>male</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O 2. 3101292</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>638</td>
          <td>0</td>
          <td>2</td>
          <td>Collyer, Mr. Harvey</td>
          <td>male</td>
          <td>31.00</td>
          <td>1</td>
          <td>1</td>
          <td>C.A. 31921</td>
          <td>26.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>639</td>
          <td>0</td>
          <td>3</td>
          <td>Panula, Mrs. Juha (Maria Emilia Ojala)</td>
          <td>female</td>
          <td>41.00</td>
          <td>0</td>
          <td>5</td>
          <td>3101295</td>
          <td>39.6875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>640</td>
          <td>0</td>
          <td>3</td>
          <td>Thorneycroft, Mr. Percival</td>
          <td>male</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>376564</td>
          <td>16.1000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>641</td>
          <td>0</td>
          <td>3</td>
          <td>Jensen, Mr. Hans Peder</td>
          <td>male</td>
          <td>20.00</td>
          <td>0</td>
          <td>0</td>
          <td>350050</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>642</td>
          <td>1</td>
          <td>1</td>
          <td>Sagesser, Mlle. Emma</td>
          <td>female</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17477</td>
          <td>69.3000</td>
          <td>B35</td>
          <td>C</td>
        </tr>
        <tr>
          <td>643</td>
          <td>0</td>
          <td>3</td>
          <td>Skoog, Miss. Margit Elizabeth</td>
          <td>female</td>
          <td>2.00</td>
          <td>3</td>
          <td>2</td>
          <td>347088</td>
          <td>27.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>644</td>
          <td>1</td>
          <td>3</td>
          <td>Foo, Mr. Choong</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>1601</td>
          <td>56.4958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>645</td>
          <td>1</td>
          <td>3</td>
          <td>Baclini, Miss. Eugenie</td>
          <td>female</td>
          <td>0.75</td>
          <td>2</td>
          <td>1</td>
          <td>2666</td>
          <td>19.2583</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>646</td>
          <td>1</td>
          <td>1</td>
          <td>Harper, Mr. Henry Sleeper</td>
          <td>male</td>
          <td>48.00</td>
          <td>1</td>
          <td>0</td>
          <td>PC 17572</td>
          <td>76.7292</td>
          <td>D33</td>
          <td>C</td>
        </tr>
        <tr>
          <td>647</td>
          <td>0</td>
          <td>3</td>
          <td>Cor, Mr. Liudevit</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>349231</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>648</td>
          <td>1</td>
          <td>1</td>
          <td>Simonius-Blumer, Col. Oberst Alfons</td>
          <td>male</td>
          <td>56.00</td>
          <td>0</td>
          <td>0</td>
          <td>13213</td>
          <td>35.5000</td>
          <td>A26</td>
          <td>C</td>
        </tr>
        <tr>
          <td>649</td>
          <td>0</td>
          <td>3</td>
          <td>Willey, Mr. Edward</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>S.O./P.P. 751</td>
          <td>7.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>650</td>
          <td>1</td>
          <td>3</td>
          <td>Stanley, Miss. Amy Zillah Elsie</td>
          <td>female</td>
          <td>23.00</td>
          <td>0</td>
          <td>0</td>
          <td>CA. 2314</td>
          <td>7.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>651</td>
          <td>0</td>
          <td>3</td>
          <td>Mitkoff, Mr. Mito</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349221</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>652</td>
          <td>1</td>
          <td>2</td>
          <td>Doling, Miss. Elsie</td>
          <td>female</td>
          <td>18.00</td>
          <td>0</td>
          <td>1</td>
          <td>231919</td>
          <td>23.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>653</td>
          <td>0</td>
          <td>3</td>
          <td>Kalvik, Mr. Johannes Halvorsen</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>8475</td>
          <td>8.4333</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>654</td>
          <td>1</td>
          <td>3</td>
          <td>O'Leary, Miss. Hanora "Norah"</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>330919</td>
          <td>7.8292</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>655</td>
          <td>0</td>
          <td>3</td>
          <td>Hegarty, Miss. Hanora "Nora"</td>
          <td>female</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>365226</td>
          <td>6.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>656</td>
          <td>0</td>
          <td>2</td>
          <td>Hickman, Mr. Leonard Mark</td>
          <td>male</td>
          <td>24.00</td>
          <td>2</td>
          <td>0</td>
          <td>S.O.C. 14879</td>
          <td>73.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>657</td>
          <td>0</td>
          <td>3</td>
          <td>Radeff, Mr. Alexander</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349223</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>658</td>
          <td>0</td>
          <td>3</td>
          <td>Bourke, Mrs. John (Catherine)</td>
          <td>female</td>
          <td>32.00</td>
          <td>1</td>
          <td>1</td>
          <td>364849</td>
          <td>15.5000</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>659</td>
          <td>0</td>
          <td>2</td>
          <td>Eitemiller, Mr. George Floyd</td>
          <td>male</td>
          <td>23.00</td>
          <td>0</td>
          <td>0</td>
          <td>29751</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>660</td>
          <td>0</td>
          <td>1</td>
          <td>Newell, Mr. Arthur Webster</td>
          <td>male</td>
          <td>58.00</td>
          <td>0</td>
          <td>2</td>
          <td>35273</td>
          <td>113.2750</td>
          <td>D48</td>
          <td>C</td>
        </tr>
        <tr>
          <td>661</td>
          <td>1</td>
          <td>1</td>
          <td>Frauenthal, Dr. Henry William</td>
          <td>male</td>
          <td>50.00</td>
          <td>2</td>
          <td>0</td>
          <td>PC 17611</td>
          <td>133.6500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>662</td>
          <td>0</td>
          <td>3</td>
          <td>Badt, Mr. Mohamed</td>
          <td>male</td>
          <td>40.00</td>
          <td>0</td>
          <td>0</td>
          <td>2623</td>
          <td>7.2250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>663</td>
          <td>0</td>
          <td>1</td>
          <td>Colley, Mr. Edward Pomeroy</td>
          <td>male</td>
          <td>47.00</td>
          <td>0</td>
          <td>0</td>
          <td>5727</td>
          <td>25.5875</td>
          <td>E58</td>
          <td>S</td>
        </tr>
        <tr>
          <td>664</td>
          <td>0</td>
          <td>3</td>
          <td>Coleff, Mr. Peju</td>
          <td>male</td>
          <td>36.00</td>
          <td>0</td>
          <td>0</td>
          <td>349210</td>
          <td>7.4958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>665</td>
          <td>1</td>
          <td>3</td>
          <td>Lindqvist, Mr. Eino William</td>
          <td>male</td>
          <td>20.00</td>
          <td>1</td>
          <td>0</td>
          <td>STON/O 2. 3101285</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>666</td>
          <td>0</td>
          <td>2</td>
          <td>Hickman, Mr. Lewis</td>
          <td>male</td>
          <td>32.00</td>
          <td>2</td>
          <td>0</td>
          <td>S.O.C. 14879</td>
          <td>73.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>667</td>
          <td>0</td>
          <td>2</td>
          <td>Butler, Mr. Reginald Fenton</td>
          <td>male</td>
          <td>25.00</td>
          <td>0</td>
          <td>0</td>
          <td>234686</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>668</td>
          <td>0</td>
          <td>3</td>
          <td>Rommetvedt, Mr. Knud Paust</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>312993</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>669</td>
          <td>0</td>
          <td>3</td>
          <td>Cook, Mr. Jacob</td>
          <td>male</td>
          <td>43.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/5 3536</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>670</td>
          <td>1</td>
          <td>1</td>
          <td>Taylor, Mrs. Elmer Zebley (Juliet Cummins Wright)</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>19996</td>
          <td>52.0000</td>
          <td>C126</td>
          <td>S</td>
        </tr>
        <tr>
          <td>671</td>
          <td>1</td>
          <td>2</td>
          <td>Brown, Mrs. Thomas William Solomon (Elizabeth Catherine Ford)</td>
          <td>female</td>
          <td>40.00</td>
          <td>1</td>
          <td>1</td>
          <td>29750</td>
          <td>39.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>672</td>
          <td>0</td>
          <td>1</td>
          <td>Davidson, Mr. Thornton</td>
          <td>male</td>
          <td>31.00</td>
          <td>1</td>
          <td>0</td>
          <td>F.C. 12750</td>
          <td>52.0000</td>
          <td>B71</td>
          <td>S</td>
        </tr>
        <tr>
          <td>673</td>
          <td>0</td>
          <td>2</td>
          <td>Mitchell, Mr. Henry Michael</td>
          <td>male</td>
          <td>70.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 24580</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>674</td>
          <td>1</td>
          <td>2</td>
          <td>Wilhelms, Mr. Charles</td>
          <td>male</td>
          <td>31.00</td>
          <td>0</td>
          <td>0</td>
          <td>244270</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>675</td>
          <td>0</td>
          <td>2</td>
          <td>Watson, Mr. Ennis Hastings</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>239856</td>
          <td>0.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>676</td>
          <td>0</td>
          <td>3</td>
          <td>Edvardsson, Mr. Gustaf Hjalmar</td>
          <td>male</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>349912</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>677</td>
          <td>0</td>
          <td>3</td>
          <td>Sawyer, Mr. Frederick Charles</td>
          <td>male</td>
          <td>24.50</td>
          <td>0</td>
          <td>0</td>
          <td>342826</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>678</td>
          <td>1</td>
          <td>3</td>
          <td>Turja, Miss. Anna Sofia</td>
          <td>female</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>4138</td>
          <td>9.8417</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>679</td>
          <td>0</td>
          <td>3</td>
          <td>Goodwin, Mrs. Frederick (Augusta Tyler)</td>
          <td>female</td>
          <td>43.00</td>
          <td>1</td>
          <td>6</td>
          <td>CA 2144</td>
          <td>46.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>680</td>
          <td>1</td>
          <td>1</td>
          <td>Cardeza, Mr. Thomas Drake Martinez</td>
          <td>male</td>
          <td>36.00</td>
          <td>0</td>
          <td>1</td>
          <td>PC 17755</td>
          <td>512.3292</td>
          <td>B51 B53 B55</td>
          <td>C</td>
        </tr>
        <tr>
          <td>681</td>
          <td>0</td>
          <td>3</td>
          <td>Peters, Miss. Katie</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>330935</td>
          <td>8.1375</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>682</td>
          <td>1</td>
          <td>1</td>
          <td>Hassab, Mr. Hammad</td>
          <td>male</td>
          <td>27.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17572</td>
          <td>76.7292</td>
          <td>D49</td>
          <td>C</td>
        </tr>
        <tr>
          <td>683</td>
          <td>0</td>
          <td>3</td>
          <td>Olsvigen, Mr. Thor Anderson</td>
          <td>male</td>
          <td>20.00</td>
          <td>0</td>
          <td>0</td>
          <td>6563</td>
          <td>9.2250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>684</td>
          <td>0</td>
          <td>3</td>
          <td>Goodwin, Mr. Charles Edward</td>
          <td>male</td>
          <td>14.00</td>
          <td>5</td>
          <td>2</td>
          <td>CA 2144</td>
          <td>46.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>685</td>
          <td>0</td>
          <td>2</td>
          <td>Brown, Mr. Thomas William Solomon</td>
          <td>male</td>
          <td>60.00</td>
          <td>1</td>
          <td>1</td>
          <td>29750</td>
          <td>39.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>686</td>
          <td>0</td>
          <td>2</td>
          <td>Laroche, Mr. Joseph Philippe Lemercier</td>
          <td>male</td>
          <td>25.00</td>
          <td>1</td>
          <td>2</td>
          <td>SC/Paris 2123</td>
          <td>41.5792</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>687</td>
          <td>0</td>
          <td>3</td>
          <td>Panula, Mr. Jaako Arnold</td>
          <td>male</td>
          <td>14.00</td>
          <td>4</td>
          <td>1</td>
          <td>3101295</td>
          <td>39.6875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>688</td>
          <td>0</td>
          <td>3</td>
          <td>Dakic, Mr. Branko</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>349228</td>
          <td>10.1708</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>689</td>
          <td>0</td>
          <td>3</td>
          <td>Fischer, Mr. Eberhard Thelander</td>
          <td>male</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>350036</td>
          <td>7.7958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>690</td>
          <td>1</td>
          <td>1</td>
          <td>Madill, Miss. Georgette Alexandra</td>
          <td>female</td>
          <td>15.00</td>
          <td>0</td>
          <td>1</td>
          <td>24160</td>
          <td>211.3375</td>
          <td>B5</td>
          <td>S</td>
        </tr>
        <tr>
          <td>691</td>
          <td>1</td>
          <td>1</td>
          <td>Dick, Mr. Albert Adrian</td>
          <td>male</td>
          <td>31.00</td>
          <td>1</td>
          <td>0</td>
          <td>17474</td>
          <td>57.0000</td>
          <td>B20</td>
          <td>S</td>
        </tr>
        <tr>
          <td>692</td>
          <td>1</td>
          <td>3</td>
          <td>Karun, Miss. Manca</td>
          <td>female</td>
          <td>4.00</td>
          <td>0</td>
          <td>1</td>
          <td>349256</td>
          <td>13.4167</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>693</td>
          <td>1</td>
          <td>3</td>
          <td>Lam, Mr. Ali</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>1601</td>
          <td>56.4958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>694</td>
          <td>0</td>
          <td>3</td>
          <td>Saad, Mr. Khalil</td>
          <td>male</td>
          <td>25.00</td>
          <td>0</td>
          <td>0</td>
          <td>2672</td>
          <td>7.2250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>695</td>
          <td>0</td>
          <td>1</td>
          <td>Weir, Col. John</td>
          <td>male</td>
          <td>60.00</td>
          <td>0</td>
          <td>0</td>
          <td>113800</td>
          <td>26.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>696</td>
          <td>0</td>
          <td>2</td>
          <td>Chapman, Mr. Charles Henry</td>
          <td>male</td>
          <td>52.00</td>
          <td>0</td>
          <td>0</td>
          <td>248731</td>
          <td>13.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>697</td>
          <td>0</td>
          <td>3</td>
          <td>Kelly, Mr. James</td>
          <td>male</td>
          <td>44.00</td>
          <td>0</td>
          <td>0</td>
          <td>363592</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>698</td>
          <td>1</td>
          <td>3</td>
          <td>Mullens, Miss. Katherine "Katie"</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>35852</td>
          <td>7.7333</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>699</td>
          <td>0</td>
          <td>1</td>
          <td>Thayer, Mr. John Borland</td>
          <td>male</td>
          <td>49.00</td>
          <td>1</td>
          <td>1</td>
          <td>17421</td>
          <td>110.8833</td>
          <td>C68</td>
          <td>C</td>
        </tr>
        <tr>
          <td>700</td>
          <td>0</td>
          <td>3</td>
          <td>Humblen, Mr. Adolf Mathias Nicolai Olsen</td>
          <td>male</td>
          <td>42.00</td>
          <td>0</td>
          <td>0</td>
          <td>348121</td>
          <td>7.6500</td>
          <td>F G63</td>
          <td>S</td>
        </tr>
        <tr>
          <td>701</td>
          <td>1</td>
          <td>1</td>
          <td>Astor, Mrs. John Jacob (Madeleine Talmadge Force)</td>
          <td>female</td>
          <td>18.00</td>
          <td>1</td>
          <td>0</td>
          <td>PC 17757</td>
          <td>227.5250</td>
          <td>C62 C64</td>
          <td>C</td>
        </tr>
        <tr>
          <td>702</td>
          <td>1</td>
          <td>1</td>
          <td>Silverthorne, Mr. Spencer Victor</td>
          <td>male</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17475</td>
          <td>26.2875</td>
          <td>E24</td>
          <td>S</td>
        </tr>
        <tr>
          <td>703</td>
          <td>0</td>
          <td>3</td>
          <td>Barbara, Miss. Saiide</td>
          <td>female</td>
          <td>18.00</td>
          <td>0</td>
          <td>1</td>
          <td>2691</td>
          <td>14.4542</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>704</td>
          <td>0</td>
          <td>3</td>
          <td>Gallagher, Mr. Martin</td>
          <td>male</td>
          <td>25.00</td>
          <td>0</td>
          <td>0</td>
          <td>36864</td>
          <td>7.7417</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>705</td>
          <td>0</td>
          <td>3</td>
          <td>Hansen, Mr. Henrik Juul</td>
          <td>male</td>
          <td>26.00</td>
          <td>1</td>
          <td>0</td>
          <td>350025</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>706</td>
          <td>0</td>
          <td>2</td>
          <td>Morley, Mr. Henry Samuel ("Mr Henry Marshall")</td>
          <td>male</td>
          <td>39.00</td>
          <td>0</td>
          <td>0</td>
          <td>250655</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>707</td>
          <td>1</td>
          <td>2</td>
          <td>Kelly, Mrs. Florence "Fannie"</td>
          <td>female</td>
          <td>45.00</td>
          <td>0</td>
          <td>0</td>
          <td>223596</td>
          <td>13.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>708</td>
          <td>1</td>
          <td>1</td>
          <td>Calderhead, Mr. Edward Pennington</td>
          <td>male</td>
          <td>42.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17476</td>
          <td>26.2875</td>
          <td>E24</td>
          <td>S</td>
        </tr>
        <tr>
          <td>709</td>
          <td>1</td>
          <td>1</td>
          <td>Cleaver, Miss. Alice</td>
          <td>female</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>113781</td>
          <td>151.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>710</td>
          <td>1</td>
          <td>3</td>
          <td>Moubarek, Master. Halim Gonios ("William George")</td>
          <td>male</td>
          <td></td>
          <td>1</td>
          <td>1</td>
          <td>2661</td>
          <td>15.2458</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>711</td>
          <td>1</td>
          <td>1</td>
          <td>Mayne, Mlle. Berthe Antonine ("Mrs de Villiers")</td>
          <td>female</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17482</td>
          <td>49.5042</td>
          <td>C90</td>
          <td>C</td>
        </tr>
        <tr>
          <td>712</td>
          <td>0</td>
          <td>1</td>
          <td>Klaber, Mr. Herman</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>113028</td>
          <td>26.5500</td>
          <td>C124</td>
          <td>S</td>
        </tr>
        <tr>
          <td>713</td>
          <td>1</td>
          <td>1</td>
          <td>Taylor, Mr. Elmer Zebley</td>
          <td>male</td>
          <td>48.00</td>
          <td>1</td>
          <td>0</td>
          <td>19996</td>
          <td>52.0000</td>
          <td>C126</td>
          <td>S</td>
        </tr>
        <tr>
          <td>714</td>
          <td>0</td>
          <td>3</td>
          <td>Larsson, Mr. August Viktor</td>
          <td>male</td>
          <td>29.00</td>
          <td>0</td>
          <td>0</td>
          <td>7545</td>
          <td>9.4833</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>715</td>
          <td>0</td>
          <td>2</td>
          <td>Greenberg, Mr. Samuel</td>
          <td>male</td>
          <td>52.00</td>
          <td>0</td>
          <td>0</td>
          <td>250647</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>716</td>
          <td>0</td>
          <td>3</td>
          <td>Soholt, Mr. Peter Andreas Lauritz Andersen</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>348124</td>
          <td>7.6500</td>
          <td>F G73</td>
          <td>S</td>
        </tr>
        <tr>
          <td>717</td>
          <td>1</td>
          <td>1</td>
          <td>Endres, Miss. Caroline Louise</td>
          <td>female</td>
          <td>38.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17757</td>
          <td>227.5250</td>
          <td>C45</td>
          <td>C</td>
        </tr>
        <tr>
          <td>718</td>
          <td>1</td>
          <td>2</td>
          <td>Troutt, Miss. Edwina Celia "Winnie"</td>
          <td>female</td>
          <td>27.00</td>
          <td>0</td>
          <td>0</td>
          <td>34218</td>
          <td>10.5000</td>
          <td>E101</td>
          <td>S</td>
        </tr>
        <tr>
          <td>719</td>
          <td>0</td>
          <td>3</td>
          <td>McEvoy, Mr. Michael</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>36568</td>
          <td>15.5000</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>720</td>
          <td>0</td>
          <td>3</td>
          <td>Johnson, Mr. Malkolm Joackim</td>
          <td>male</td>
          <td>33.00</td>
          <td>0</td>
          <td>0</td>
          <td>347062</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>721</td>
          <td>1</td>
          <td>2</td>
          <td>Harper, Miss. Annie Jessie "Nina"</td>
          <td>female</td>
          <td>6.00</td>
          <td>0</td>
          <td>1</td>
          <td>248727</td>
          <td>33.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>722</td>
          <td>0</td>
          <td>3</td>
          <td>Jensen, Mr. Svend Lauritz</td>
          <td>male</td>
          <td>17.00</td>
          <td>1</td>
          <td>0</td>
          <td>350048</td>
          <td>7.0542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>723</td>
          <td>0</td>
          <td>2</td>
          <td>Gillespie, Mr. William Henry</td>
          <td>male</td>
          <td>34.00</td>
          <td>0</td>
          <td>0</td>
          <td>12233</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>724</td>
          <td>0</td>
          <td>2</td>
          <td>Hodges, Mr. Henry Price</td>
          <td>male</td>
          <td>50.00</td>
          <td>0</td>
          <td>0</td>
          <td>250643</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>725</td>
          <td>1</td>
          <td>1</td>
          <td>Chambers, Mr. Norman Campbell</td>
          <td>male</td>
          <td>27.00</td>
          <td>1</td>
          <td>0</td>
          <td>113806</td>
          <td>53.1000</td>
          <td>E8</td>
          <td>S</td>
        </tr>
        <tr>
          <td>726</td>
          <td>0</td>
          <td>3</td>
          <td>Oreskovic, Mr. Luka</td>
          <td>male</td>
          <td>20.00</td>
          <td>0</td>
          <td>0</td>
          <td>315094</td>
          <td>8.6625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>727</td>
          <td>1</td>
          <td>2</td>
          <td>Renouf, Mrs. Peter Henry (Lillian Jefferys)</td>
          <td>female</td>
          <td>30.00</td>
          <td>3</td>
          <td>0</td>
          <td>31027</td>
          <td>21.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>728</td>
          <td>1</td>
          <td>3</td>
          <td>Mannion, Miss. Margareth</td>
          <td>female</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>36866</td>
          <td>7.7375</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>729</td>
          <td>0</td>
          <td>2</td>
          <td>Bryhl, Mr. Kurt Arnold Gottfrid</td>
          <td>male</td>
          <td>25.00</td>
          <td>1</td>
          <td>0</td>
          <td>236853</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>730</td>
          <td>0</td>
          <td>3</td>
          <td>Ilmakangas, Miss. Pieta Sofia</td>
          <td>female</td>
          <td>25.00</td>
          <td>1</td>
          <td>0</td>
          <td>STON/O2. 3101271</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>731</td>
          <td>1</td>
          <td>1</td>
          <td>Allen, Miss. Elisabeth Walton</td>
          <td>female</td>
          <td>29.00</td>
          <td>0</td>
          <td>0</td>
          <td>24160</td>
          <td>211.3375</td>
          <td>B5</td>
          <td>S</td>
        </tr>
        <tr>
          <td>732</td>
          <td>0</td>
          <td>3</td>
          <td>Hassan, Mr. Houssein G N</td>
          <td>male</td>
          <td>11.00</td>
          <td>0</td>
          <td>0</td>
          <td>2699</td>
          <td>18.7875</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>733</td>
          <td>0</td>
          <td>2</td>
          <td>Knight, Mr. Robert J</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>239855</td>
          <td>0.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>734</td>
          <td>0</td>
          <td>2</td>
          <td>Berriman, Mr. William John</td>
          <td>male</td>
          <td>23.00</td>
          <td>0</td>
          <td>0</td>
          <td>28425</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>735</td>
          <td>0</td>
          <td>2</td>
          <td>Troupiansky, Mr. Moses Aaron</td>
          <td>male</td>
          <td>23.00</td>
          <td>0</td>
          <td>0</td>
          <td>233639</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>736</td>
          <td>0</td>
          <td>3</td>
          <td>Williams, Mr. Leslie</td>
          <td>male</td>
          <td>28.50</td>
          <td>0</td>
          <td>0</td>
          <td>54636</td>
          <td>16.1000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>737</td>
          <td>0</td>
          <td>3</td>
          <td>Ford, Mrs. Edward (Margaret Ann Watson)</td>
          <td>female</td>
          <td>48.00</td>
          <td>1</td>
          <td>3</td>
          <td>W./C. 6608</td>
          <td>34.3750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>738</td>
          <td>1</td>
          <td>1</td>
          <td>Lesurer, Mr. Gustave J</td>
          <td>male</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17755</td>
          <td>512.3292</td>
          <td>B101</td>
          <td>C</td>
        </tr>
        <tr>
          <td>739</td>
          <td>0</td>
          <td>3</td>
          <td>Ivanoff, Mr. Kanio</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349201</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>740</td>
          <td>0</td>
          <td>3</td>
          <td>Nankoff, Mr. Minko</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349218</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>741</td>
          <td>1</td>
          <td>1</td>
          <td>Hawksford, Mr. Walter James</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>16988</td>
          <td>30.0000</td>
          <td>D45</td>
          <td>S</td>
        </tr>
        <tr>
          <td>742</td>
          <td>0</td>
          <td>1</td>
          <td>Cavendish, Mr. Tyrell William</td>
          <td>male</td>
          <td>36.00</td>
          <td>1</td>
          <td>0</td>
          <td>19877</td>
          <td>78.8500</td>
          <td>C46</td>
          <td>S</td>
        </tr>
        <tr>
          <td>743</td>
          <td>1</td>
          <td>1</td>
          <td>Ryerson, Miss. Susan Parker "Suzette"</td>
          <td>female</td>
          <td>21.00</td>
          <td>2</td>
          <td>2</td>
          <td>PC 17608</td>
          <td>262.3750</td>
          <td>B57 B59 B63 B66</td>
          <td>C</td>
        </tr>
        <tr>
          <td>744</td>
          <td>0</td>
          <td>3</td>
          <td>McNamee, Mr. Neal</td>
          <td>male</td>
          <td>24.00</td>
          <td>1</td>
          <td>0</td>
          <td>376566</td>
          <td>16.1000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>745</td>
          <td>1</td>
          <td>3</td>
          <td>Stranden, Mr. Juho</td>
          <td>male</td>
          <td>31.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O 2. 3101288</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>746</td>
          <td>0</td>
          <td>1</td>
          <td>Crosby, Capt. Edward Gifford</td>
          <td>male</td>
          <td>70.00</td>
          <td>1</td>
          <td>1</td>
          <td>WE/P 5735</td>
          <td>71.0000</td>
          <td>B22</td>
          <td>S</td>
        </tr>
        <tr>
          <td>747</td>
          <td>0</td>
          <td>3</td>
          <td>Abbott, Mr. Rossmore Edward</td>
          <td>male</td>
          <td>16.00</td>
          <td>1</td>
          <td>1</td>
          <td>C.A. 2673</td>
          <td>20.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>748</td>
          <td>1</td>
          <td>2</td>
          <td>Sinkkonen, Miss. Anna</td>
          <td>female</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>250648</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>749</td>
          <td>0</td>
          <td>1</td>
          <td>Marvin, Mr. Daniel Warner</td>
          <td>male</td>
          <td>19.00</td>
          <td>1</td>
          <td>0</td>
          <td>113773</td>
          <td>53.1000</td>
          <td>D30</td>
          <td>S</td>
        </tr>
        <tr>
          <td>750</td>
          <td>0</td>
          <td>3</td>
          <td>Connaghton, Mr. Michael</td>
          <td>male</td>
          <td>31.00</td>
          <td>0</td>
          <td>0</td>
          <td>335097</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>751</td>
          <td>1</td>
          <td>2</td>
          <td>Wells, Miss. Joan</td>
          <td>female</td>
          <td>4.00</td>
          <td>1</td>
          <td>1</td>
          <td>29103</td>
          <td>23.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>752</td>
          <td>1</td>
          <td>3</td>
          <td>Moor, Master. Meier</td>
          <td>male</td>
          <td>6.00</td>
          <td>0</td>
          <td>1</td>
          <td>392096</td>
          <td>12.4750</td>
          <td>E121</td>
          <td>S</td>
        </tr>
        <tr>
          <td>753</td>
          <td>0</td>
          <td>3</td>
          <td>Vande Velde, Mr. Johannes Joseph</td>
          <td>male</td>
          <td>33.00</td>
          <td>0</td>
          <td>0</td>
          <td>345780</td>
          <td>9.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>754</td>
          <td>0</td>
          <td>3</td>
          <td>Jonkoff, Mr. Lalio</td>
          <td>male</td>
          <td>23.00</td>
          <td>0</td>
          <td>0</td>
          <td>349204</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>755</td>
          <td>1</td>
          <td>2</td>
          <td>Herman, Mrs. Samuel (Jane Laver)</td>
          <td>female</td>
          <td>48.00</td>
          <td>1</td>
          <td>2</td>
          <td>220845</td>
          <td>65.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>756</td>
          <td>1</td>
          <td>2</td>
          <td>Hamalainen, Master. Viljo</td>
          <td>male</td>
          <td>0.67</td>
          <td>1</td>
          <td>1</td>
          <td>250649</td>
          <td>14.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>757</td>
          <td>0</td>
          <td>3</td>
          <td>Carlsson, Mr. August Sigfrid</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>350042</td>
          <td>7.7958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>758</td>
          <td>0</td>
          <td>2</td>
          <td>Bailey, Mr. Percy Andrew</td>
          <td>male</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>29108</td>
          <td>11.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>759</td>
          <td>0</td>
          <td>3</td>
          <td>Theobald, Mr. Thomas Leonard</td>
          <td>male</td>
          <td>34.00</td>
          <td>0</td>
          <td>0</td>
          <td>363294</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>760</td>
          <td>1</td>
          <td>1</td>
          <td>Rothes, the Countess. of (Lucy Noel Martha Dyer-Edwards)</td>
          <td>female</td>
          <td>33.00</td>
          <td>0</td>
          <td>0</td>
          <td>110152</td>
          <td>86.5000</td>
          <td>B77</td>
          <td>S</td>
        </tr>
        <tr>
          <td>761</td>
          <td>0</td>
          <td>3</td>
          <td>Garfirth, Mr. John</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>358585</td>
          <td>14.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>762</td>
          <td>0</td>
          <td>3</td>
          <td>Nirva, Mr. Iisakki Antino Aijo</td>
          <td>male</td>
          <td>41.00</td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/O2 3101272</td>
          <td>7.1250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>763</td>
          <td>1</td>
          <td>3</td>
          <td>Barah, Mr. Hanna Assi</td>
          <td>male</td>
          <td>20.00</td>
          <td>0</td>
          <td>0</td>
          <td>2663</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>764</td>
          <td>1</td>
          <td>1</td>
          <td>Carter, Mrs. William Ernest (Lucile Polk)</td>
          <td>female</td>
          <td>36.00</td>
          <td>1</td>
          <td>2</td>
          <td>113760</td>
          <td>120.0000</td>
          <td>B96 B98</td>
          <td>S</td>
        </tr>
        <tr>
          <td>765</td>
          <td>0</td>
          <td>3</td>
          <td>Eklund, Mr. Hans Linus</td>
          <td>male</td>
          <td>16.00</td>
          <td>0</td>
          <td>0</td>
          <td>347074</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>766</td>
          <td>1</td>
          <td>1</td>
          <td>Hogeboom, Mrs. John C (Anna Andrews)</td>
          <td>female</td>
          <td>51.00</td>
          <td>1</td>
          <td>0</td>
          <td>13502</td>
          <td>77.9583</td>
          <td>D11</td>
          <td>S</td>
        </tr>
        <tr>
          <td>767</td>
          <td>0</td>
          <td>1</td>
          <td>Brewe, Dr. Arthur Jackson</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>112379</td>
          <td>39.6000</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>768</td>
          <td>0</td>
          <td>3</td>
          <td>Mangan, Miss. Mary</td>
          <td>female</td>
          <td>30.50</td>
          <td>0</td>
          <td>0</td>
          <td>364850</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>769</td>
          <td>0</td>
          <td>3</td>
          <td>Moran, Mr. Daniel J</td>
          <td>male</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>371110</td>
          <td>24.1500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>770</td>
          <td>0</td>
          <td>3</td>
          <td>Gronnestad, Mr. Daniel Danielsen</td>
          <td>male</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>8471</td>
          <td>8.3625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>771</td>
          <td>0</td>
          <td>3</td>
          <td>Lievens, Mr. Rene Aime</td>
          <td>male</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>345781</td>
          <td>9.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>772</td>
          <td>0</td>
          <td>3</td>
          <td>Jensen, Mr. Niels Peder</td>
          <td>male</td>
          <td>48.00</td>
          <td>0</td>
          <td>0</td>
          <td>350047</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>773</td>
          <td>0</td>
          <td>2</td>
          <td>Mack, Mrs. (Mary)</td>
          <td>female</td>
          <td>57.00</td>
          <td>0</td>
          <td>0</td>
          <td>S.O./P.P. 3</td>
          <td>10.5000</td>
          <td>E77</td>
          <td>S</td>
        </tr>
        <tr>
          <td>774</td>
          <td>0</td>
          <td>3</td>
          <td>Elias, Mr. Dibo</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2674</td>
          <td>7.2250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>775</td>
          <td>1</td>
          <td>2</td>
          <td>Hocking, Mrs. Elizabeth (Eliza Needs)</td>
          <td>female</td>
          <td>54.00</td>
          <td>1</td>
          <td>3</td>
          <td>29105</td>
          <td>23.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>776</td>
          <td>0</td>
          <td>3</td>
          <td>Myhrman, Mr. Pehr Fabian Oliver Malkolm</td>
          <td>male</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>347078</td>
          <td>7.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>777</td>
          <td>0</td>
          <td>3</td>
          <td>Tobin, Mr. Roger</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>383121</td>
          <td>7.7500</td>
          <td>F38</td>
          <td>Q</td>
        </tr>
        <tr>
          <td>778</td>
          <td>1</td>
          <td>3</td>
          <td>Emanuel, Miss. Virginia Ethel</td>
          <td>female</td>
          <td>5.00</td>
          <td>0</td>
          <td>0</td>
          <td>364516</td>
          <td>12.4750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>779</td>
          <td>0</td>
          <td>3</td>
          <td>Kilgannon, Mr. Thomas J</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>36865</td>
          <td>7.7375</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>780</td>
          <td>1</td>
          <td>1</td>
          <td>Robert, Mrs. Edward Scott (Elisabeth Walton McMillan)</td>
          <td>female</td>
          <td>43.00</td>
          <td>0</td>
          <td>1</td>
          <td>24160</td>
          <td>211.3375</td>
          <td>B3</td>
          <td>S</td>
        </tr>
        <tr>
          <td>781</td>
          <td>1</td>
          <td>3</td>
          <td>Ayoub, Miss. Banoura</td>
          <td>female</td>
          <td>13.00</td>
          <td>0</td>
          <td>0</td>
          <td>2687</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>782</td>
          <td>1</td>
          <td>1</td>
          <td>Dick, Mrs. Albert Adrian (Vera Gillespie)</td>
          <td>female</td>
          <td>17.00</td>
          <td>1</td>
          <td>0</td>
          <td>17474</td>
          <td>57.0000</td>
          <td>B20</td>
          <td>S</td>
        </tr>
        <tr>
          <td>783</td>
          <td>0</td>
          <td>1</td>
          <td>Long, Mr. Milton Clyde</td>
          <td>male</td>
          <td>29.00</td>
          <td>0</td>
          <td>0</td>
          <td>113501</td>
          <td>30.0000</td>
          <td>D6</td>
          <td>S</td>
        </tr>
        <tr>
          <td>784</td>
          <td>0</td>
          <td>3</td>
          <td>Johnston, Mr. Andrew G</td>
          <td>male</td>
          <td></td>
          <td>1</td>
          <td>2</td>
          <td>W./C. 6607</td>
          <td>23.4500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>785</td>
          <td>0</td>
          <td>3</td>
          <td>Ali, Mr. William</td>
          <td>male</td>
          <td>25.00</td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/O.Q. 3101312</td>
          <td>7.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>786</td>
          <td>0</td>
          <td>3</td>
          <td>Harmer, Mr. Abraham (David Lishin)</td>
          <td>male</td>
          <td>25.00</td>
          <td>0</td>
          <td>0</td>
          <td>374887</td>
          <td>7.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>787</td>
          <td>1</td>
          <td>3</td>
          <td>Sjoblom, Miss. Anna Sofia</td>
          <td>female</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>3101265</td>
          <td>7.4958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>788</td>
          <td>0</td>
          <td>3</td>
          <td>Rice, Master. George Hugh</td>
          <td>male</td>
          <td>8.00</td>
          <td>4</td>
          <td>1</td>
          <td>382652</td>
          <td>29.1250</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>789</td>
          <td>1</td>
          <td>3</td>
          <td>Dean, Master. Bertram Vere</td>
          <td>male</td>
          <td>1.00</td>
          <td>1</td>
          <td>2</td>
          <td>C.A. 2315</td>
          <td>20.5750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>790</td>
          <td>0</td>
          <td>1</td>
          <td>Guggenheim, Mr. Benjamin</td>
          <td>male</td>
          <td>46.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17593</td>
          <td>79.2000</td>
          <td>B82 B84</td>
          <td>C</td>
        </tr>
        <tr>
          <td>791</td>
          <td>0</td>
          <td>3</td>
          <td>Keane, Mr. Andrew "Andy"</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>12460</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>792</td>
          <td>0</td>
          <td>2</td>
          <td>Gaskell, Mr. Alfred</td>
          <td>male</td>
          <td>16.00</td>
          <td>0</td>
          <td>0</td>
          <td>239865</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>793</td>
          <td>0</td>
          <td>3</td>
          <td>Sage, Miss. Stella Anna</td>
          <td>female</td>
          <td></td>
          <td>8</td>
          <td>2</td>
          <td>CA. 2343</td>
          <td>69.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>794</td>
          <td>0</td>
          <td>1</td>
          <td>Hoyt, Mr. William Fisher</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>PC 17600</td>
          <td>30.6958</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>795</td>
          <td>0</td>
          <td>3</td>
          <td>Dantcheff, Mr. Ristiu</td>
          <td>male</td>
          <td>25.00</td>
          <td>0</td>
          <td>0</td>
          <td>349203</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>796</td>
          <td>0</td>
          <td>2</td>
          <td>Otter, Mr. Richard</td>
          <td>male</td>
          <td>39.00</td>
          <td>0</td>
          <td>0</td>
          <td>28213</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>797</td>
          <td>1</td>
          <td>1</td>
          <td>Leader, Dr. Alice (Farnham)</td>
          <td>female</td>
          <td>49.00</td>
          <td>0</td>
          <td>0</td>
          <td>17465</td>
          <td>25.9292</td>
          <td>D17</td>
          <td>S</td>
        </tr>
        <tr>
          <td>798</td>
          <td>1</td>
          <td>3</td>
          <td>Osman, Mrs. Mara</td>
          <td>female</td>
          <td>31.00</td>
          <td>0</td>
          <td>0</td>
          <td>349244</td>
          <td>8.6833</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>799</td>
          <td>0</td>
          <td>3</td>
          <td>Ibrahim Shawah, Mr. Yousseff</td>
          <td>male</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>2685</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>800</td>
          <td>0</td>
          <td>3</td>
          <td>Van Impe, Mrs. Jean Baptiste (Rosalie Paula Govaert)</td>
          <td>female</td>
          <td>30.00</td>
          <td>1</td>
          <td>1</td>
          <td>345773</td>
          <td>24.1500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>801</td>
          <td>0</td>
          <td>2</td>
          <td>Ponesell, Mr. Martin</td>
          <td>male</td>
          <td>34.00</td>
          <td>0</td>
          <td>0</td>
          <td>250647</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>802</td>
          <td>1</td>
          <td>2</td>
          <td>Collyer, Mrs. Harvey (Charlotte Annie Tate)</td>
          <td>female</td>
          <td>31.00</td>
          <td>1</td>
          <td>1</td>
          <td>C.A. 31921</td>
          <td>26.2500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>803</td>
          <td>1</td>
          <td>1</td>
          <td>Carter, Master. William Thornton II</td>
          <td>male</td>
          <td>11.00</td>
          <td>1</td>
          <td>2</td>
          <td>113760</td>
          <td>120.0000</td>
          <td>B96 B98</td>
          <td>S</td>
        </tr>
        <tr>
          <td>804</td>
          <td>1</td>
          <td>3</td>
          <td>Thomas, Master. Assad Alexander</td>
          <td>male</td>
          <td>0.42</td>
          <td>0</td>
          <td>1</td>
          <td>2625</td>
          <td>8.5167</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>805</td>
          <td>1</td>
          <td>3</td>
          <td>Hedman, Mr. Oskar Arvid</td>
          <td>male</td>
          <td>27.00</td>
          <td>0</td>
          <td>0</td>
          <td>347089</td>
          <td>6.9750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>806</td>
          <td>0</td>
          <td>3</td>
          <td>Johansson, Mr. Karl Johan</td>
          <td>male</td>
          <td>31.00</td>
          <td>0</td>
          <td>0</td>
          <td>347063</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>807</td>
          <td>0</td>
          <td>1</td>
          <td>Andrews, Mr. Thomas Jr</td>
          <td>male</td>
          <td>39.00</td>
          <td>0</td>
          <td>0</td>
          <td>112050</td>
          <td>0.0000</td>
          <td>A36</td>
          <td>S</td>
        </tr>
        <tr>
          <td>808</td>
          <td>0</td>
          <td>3</td>
          <td>Pettersson, Miss. Ellen Natalia</td>
          <td>female</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>347087</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>809</td>
          <td>0</td>
          <td>2</td>
          <td>Meyer, Mr. August</td>
          <td>male</td>
          <td>39.00</td>
          <td>0</td>
          <td>0</td>
          <td>248723</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>810</td>
          <td>1</td>
          <td>1</td>
          <td>Chambers, Mrs. Norman Campbell (Bertha Griggs)</td>
          <td>female</td>
          <td>33.00</td>
          <td>1</td>
          <td>0</td>
          <td>113806</td>
          <td>53.1000</td>
          <td>E8</td>
          <td>S</td>
        </tr>
        <tr>
          <td>811</td>
          <td>0</td>
          <td>3</td>
          <td>Alexander, Mr. William</td>
          <td>male</td>
          <td>26.00</td>
          <td>0</td>
          <td>0</td>
          <td>3474</td>
          <td>7.8875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>812</td>
          <td>0</td>
          <td>3</td>
          <td>Lester, Mr. James</td>
          <td>male</td>
          <td>39.00</td>
          <td>0</td>
          <td>0</td>
          <td>A/4 48871</td>
          <td>24.1500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>813</td>
          <td>0</td>
          <td>2</td>
          <td>Slemen, Mr. Richard James</td>
          <td>male</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>28206</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>814</td>
          <td>0</td>
          <td>3</td>
          <td>Andersson, Miss. Ebba Iris Alfrida</td>
          <td>female</td>
          <td>6.00</td>
          <td>4</td>
          <td>2</td>
          <td>347082</td>
          <td>31.2750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>815</td>
          <td>0</td>
          <td>3</td>
          <td>Tomlin, Mr. Ernest Portage</td>
          <td>male</td>
          <td>30.50</td>
          <td>0</td>
          <td>0</td>
          <td>364499</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>816</td>
          <td>0</td>
          <td>1</td>
          <td>Fry, Mr. Richard</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>112058</td>
          <td>0.0000</td>
          <td>B102</td>
          <td>S</td>
        </tr>
        <tr>
          <td>817</td>
          <td>0</td>
          <td>3</td>
          <td>Heininen, Miss. Wendla Maria</td>
          <td>female</td>
          <td>23.00</td>
          <td>0</td>
          <td>0</td>
          <td>STON/O2. 3101290</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>818</td>
          <td>0</td>
          <td>2</td>
          <td>Mallet, Mr. Albert</td>
          <td>male</td>
          <td>31.00</td>
          <td>1</td>
          <td>1</td>
          <td>S.C./PARIS 2079</td>
          <td>37.0042</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>819</td>
          <td>0</td>
          <td>3</td>
          <td>Holm, Mr. John Fredrik Alexander</td>
          <td>male</td>
          <td>43.00</td>
          <td>0</td>
          <td>0</td>
          <td>C 7075</td>
          <td>6.4500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>820</td>
          <td>0</td>
          <td>3</td>
          <td>Skoog, Master. Karl Thorsten</td>
          <td>male</td>
          <td>10.00</td>
          <td>3</td>
          <td>2</td>
          <td>347088</td>
          <td>27.9000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>821</td>
          <td>1</td>
          <td>1</td>
          <td>Hays, Mrs. Charles Melville (Clara Jennings Gregg)</td>
          <td>female</td>
          <td>52.00</td>
          <td>1</td>
          <td>1</td>
          <td>12749</td>
          <td>93.5000</td>
          <td>B69</td>
          <td>S</td>
        </tr>
        <tr>
          <td>822</td>
          <td>1</td>
          <td>3</td>
          <td>Lulic, Mr. Nikola</td>
          <td>male</td>
          <td>27.00</td>
          <td>0</td>
          <td>0</td>
          <td>315098</td>
          <td>8.6625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>823</td>
          <td>0</td>
          <td>1</td>
          <td>Reuchlin, Jonkheer. John George</td>
          <td>male</td>
          <td>38.00</td>
          <td>0</td>
          <td>0</td>
          <td>19972</td>
          <td>0.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>824</td>
          <td>1</td>
          <td>3</td>
          <td>Moor, Mrs. (Beila)</td>
          <td>female</td>
          <td>27.00</td>
          <td>0</td>
          <td>1</td>
          <td>392096</td>
          <td>12.4750</td>
          <td>E121</td>
          <td>S</td>
        </tr>
        <tr>
          <td>825</td>
          <td>0</td>
          <td>3</td>
          <td>Panula, Master. Urho Abraham</td>
          <td>male</td>
          <td>2.00</td>
          <td>4</td>
          <td>1</td>
          <td>3101295</td>
          <td>39.6875</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>826</td>
          <td>0</td>
          <td>3</td>
          <td>Flynn, Mr. John</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>368323</td>
          <td>6.9500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>827</td>
          <td>0</td>
          <td>3</td>
          <td>Lam, Mr. Len</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>1601</td>
          <td>56.4958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>828</td>
          <td>1</td>
          <td>2</td>
          <td>Mallet, Master. Andre</td>
          <td>male</td>
          <td>1.00</td>
          <td>0</td>
          <td>2</td>
          <td>S.C./PARIS 2079</td>
          <td>37.0042</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>829</td>
          <td>1</td>
          <td>3</td>
          <td>McCormack, Mr. Thomas Joseph</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>367228</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>830</td>
          <td>1</td>
          <td>1</td>
          <td>Stone, Mrs. George Nelson (Martha Evelyn)</td>
          <td>female</td>
          <td>62.00</td>
          <td>0</td>
          <td>0</td>
          <td>113572</td>
          <td>80.0000</td>
          <td>B28</td>
          <td></td>
        </tr>
        <tr>
          <td>831</td>
          <td>1</td>
          <td>3</td>
          <td>Yasbeck, Mrs. Antoni (Selini Alexander)</td>
          <td>female</td>
          <td>15.00</td>
          <td>1</td>
          <td>0</td>
          <td>2659</td>
          <td>14.4542</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>832</td>
          <td>1</td>
          <td>2</td>
          <td>Richards, Master. George Sibley</td>
          <td>male</td>
          <td>0.83</td>
          <td>1</td>
          <td>1</td>
          <td>29106</td>
          <td>18.7500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>833</td>
          <td>0</td>
          <td>3</td>
          <td>Saad, Mr. Amin</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2671</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>834</td>
          <td>0</td>
          <td>3</td>
          <td>Augustsson, Mr. Albert</td>
          <td>male</td>
          <td>23.00</td>
          <td>0</td>
          <td>0</td>
          <td>347468</td>
          <td>7.8542</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>835</td>
          <td>0</td>
          <td>3</td>
          <td>Allum, Mr. Owen George</td>
          <td>male</td>
          <td>18.00</td>
          <td>0</td>
          <td>0</td>
          <td>2223</td>
          <td>8.3000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>836</td>
          <td>1</td>
          <td>1</td>
          <td>Compton, Miss. Sara Rebecca</td>
          <td>female</td>
          <td>39.00</td>
          <td>1</td>
          <td>1</td>
          <td>PC 17756</td>
          <td>83.1583</td>
          <td>E49</td>
          <td>C</td>
        </tr>
        <tr>
          <td>837</td>
          <td>0</td>
          <td>3</td>
          <td>Pasic, Mr. Jakob</td>
          <td>male</td>
          <td>21.00</td>
          <td>0</td>
          <td>0</td>
          <td>315097</td>
          <td>8.6625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>838</td>
          <td>0</td>
          <td>3</td>
          <td>Sirota, Mr. Maurice</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>392092</td>
          <td>8.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>839</td>
          <td>1</td>
          <td>3</td>
          <td>Chip, Mr. Chang</td>
          <td>male</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>1601</td>
          <td>56.4958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>840</td>
          <td>1</td>
          <td>1</td>
          <td>Marechal, Mr. Pierre</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>11774</td>
          <td>29.7000</td>
          <td>C47</td>
          <td>C</td>
        </tr>
        <tr>
          <td>841</td>
          <td>0</td>
          <td>3</td>
          <td>Alhomaki, Mr. Ilmari Rudolf</td>
          <td>male</td>
          <td>20.00</td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/O2 3101287</td>
          <td>7.9250</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>842</td>
          <td>0</td>
          <td>2</td>
          <td>Mudd, Mr. Thomas Charles</td>
          <td>male</td>
          <td>16.00</td>
          <td>0</td>
          <td>0</td>
          <td>S.O./P.P. 3</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>843</td>
          <td>1</td>
          <td>1</td>
          <td>Serepeca, Miss. Augusta</td>
          <td>female</td>
          <td>30.00</td>
          <td>0</td>
          <td>0</td>
          <td>113798</td>
          <td>31.0000</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>844</td>
          <td>0</td>
          <td>3</td>
          <td>Lemberopolous, Mr. Peter L</td>
          <td>male</td>
          <td>34.50</td>
          <td>0</td>
          <td>0</td>
          <td>2683</td>
          <td>6.4375</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>845</td>
          <td>0</td>
          <td>3</td>
          <td>Culumovic, Mr. Jeso</td>
          <td>male</td>
          <td>17.00</td>
          <td>0</td>
          <td>0</td>
          <td>315090</td>
          <td>8.6625</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>846</td>
          <td>0</td>
          <td>3</td>
          <td>Abbing, Mr. Anthony</td>
          <td>male</td>
          <td>42.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A. 5547</td>
          <td>7.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>847</td>
          <td>0</td>
          <td>3</td>
          <td>Sage, Mr. Douglas Bullen</td>
          <td>male</td>
          <td></td>
          <td>8</td>
          <td>2</td>
          <td>CA. 2343</td>
          <td>69.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>848</td>
          <td>0</td>
          <td>3</td>
          <td>Markoff, Mr. Marin</td>
          <td>male</td>
          <td>35.00</td>
          <td>0</td>
          <td>0</td>
          <td>349213</td>
          <td>7.8958</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>849</td>
          <td>0</td>
          <td>2</td>
          <td>Harper, Rev. John</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>1</td>
          <td>248727</td>
          <td>33.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>850</td>
          <td>1</td>
          <td>1</td>
          <td>Goldenberg, Mrs. Samuel L (Edwiga Grabowska)</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>0</td>
          <td>17453</td>
          <td>89.1042</td>
          <td>C92</td>
          <td>C</td>
        </tr>
        <tr>
          <td>851</td>
          <td>0</td>
          <td>3</td>
          <td>Andersson, Master. Sigvard Harald Elias</td>
          <td>male</td>
          <td>4.00</td>
          <td>4</td>
          <td>2</td>
          <td>347082</td>
          <td>31.2750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>852</td>
          <td>0</td>
          <td>3</td>
          <td>Svensson, Mr. Johan</td>
          <td>male</td>
          <td>74.00</td>
          <td>0</td>
          <td>0</td>
          <td>347060</td>
          <td>7.7750</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>853</td>
          <td>0</td>
          <td>3</td>
          <td>Boulos, Miss. Nourelain</td>
          <td>female</td>
          <td>9.00</td>
          <td>1</td>
          <td>1</td>
          <td>2678</td>
          <td>15.2458</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>854</td>
          <td>1</td>
          <td>1</td>
          <td>Lines, Miss. Mary Conover</td>
          <td>female</td>
          <td>16.00</td>
          <td>0</td>
          <td>1</td>
          <td>PC 17592</td>
          <td>39.4000</td>
          <td>D28</td>
          <td>S</td>
        </tr>
        <tr>
          <td>855</td>
          <td>0</td>
          <td>2</td>
          <td>Carter, Mrs. Ernest Courtenay (Lilian Hughes)</td>
          <td>female</td>
          <td>44.00</td>
          <td>1</td>
          <td>0</td>
          <td>244252</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>856</td>
          <td>1</td>
          <td>3</td>
          <td>Aks, Mrs. Sam (Leah Rosen)</td>
          <td>female</td>
          <td>18.00</td>
          <td>0</td>
          <td>1</td>
          <td>392091</td>
          <td>9.3500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>857</td>
          <td>1</td>
          <td>1</td>
          <td>Wick, Mrs. George Dennick (Mary Hitchcock)</td>
          <td>female</td>
          <td>45.00</td>
          <td>1</td>
          <td>1</td>
          <td>36928</td>
          <td>164.8667</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>858</td>
          <td>1</td>
          <td>1</td>
          <td>Daly, Mr. Peter Denis</td>
          <td>male</td>
          <td>51.00</td>
          <td>0</td>
          <td>0</td>
          <td>113055</td>
          <td>26.5500</td>
          <td>E17</td>
          <td>S</td>
        </tr>
        <tr>
          <td>859</td>
          <td>1</td>
          <td>3</td>
          <td>Baclini, Mrs. Solomon (Latifa Qurban)</td>
          <td>female</td>
          <td>24.00</td>
          <td>0</td>
          <td>3</td>
          <td>2666</td>
          <td>19.2583</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>860</td>
          <td>0</td>
          <td>3</td>
          <td>Razi, Mr. Raihed</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>2629</td>
          <td>7.2292</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>861</td>
          <td>0</td>
          <td>3</td>
          <td>Hansen, Mr. Claus Peter</td>
          <td>male</td>
          <td>41.00</td>
          <td>2</td>
          <td>0</td>
          <td>350026</td>
          <td>14.1083</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>862</td>
          <td>0</td>
          <td>2</td>
          <td>Giles, Mr. Frederick Edward</td>
          <td>male</td>
          <td>21.00</td>
          <td>1</td>
          <td>0</td>
          <td>28134</td>
          <td>11.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>863</td>
          <td>1</td>
          <td>1</td>
          <td>Swift, Mrs. Frederick Joel (Margaret Welles Barron)</td>
          <td>female</td>
          <td>48.00</td>
          <td>0</td>
          <td>0</td>
          <td>17466</td>
          <td>25.9292</td>
          <td>D17</td>
          <td>S</td>
        </tr>
        <tr>
          <td>864</td>
          <td>0</td>
          <td>3</td>
          <td>Sage, Miss. Dorothy Edith "Dolly"</td>
          <td>female</td>
          <td></td>
          <td>8</td>
          <td>2</td>
          <td>CA. 2343</td>
          <td>69.5500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>865</td>
          <td>0</td>
          <td>2</td>
          <td>Gill, Mr. John William</td>
          <td>male</td>
          <td>24.00</td>
          <td>0</td>
          <td>0</td>
          <td>233866</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>866</td>
          <td>1</td>
          <td>2</td>
          <td>Bystrom, Mrs. (Karolina)</td>
          <td>female</td>
          <td>42.00</td>
          <td>0</td>
          <td>0</td>
          <td>236852</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>867</td>
          <td>1</td>
          <td>2</td>
          <td>Duran y More, Miss. Asuncion</td>
          <td>female</td>
          <td>27.00</td>
          <td>1</td>
          <td>0</td>
          <td>SC/PARIS 2149</td>
          <td>13.8583</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>868</td>
          <td>0</td>
          <td>1</td>
          <td>Roebling, Mr. Washington Augustus II</td>
          <td>male</td>
          <td>31.00</td>
          <td>0</td>
          <td>0</td>
          <td>PC 17590</td>
          <td>50.4958</td>
          <td>A24</td>
          <td>S</td>
        </tr>
        <tr>
          <td>869</td>
          <td>0</td>
          <td>3</td>
          <td>van Melkebeke, Mr. Philemon</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>345777</td>
          <td>9.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>870</td>
          <td>1</td>
          <td>3</td>
          <td>Johnson, Master. Harold Theodor</td>
          <td>male</td>
          <td>4.00</td>
          <td>1</td>
          <td>1</td>
          <td>347742</td>
          <td>11.1333</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>871</td>
          <td>0</td>
          <td>3</td>
          <td>Balkic, Mr. Cerin</td>
          <td>male</td>
          <td>26.00</td>
          <td>0</td>
          <td>0</td>
          <td>349248</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>872</td>
          <td>1</td>
          <td>1</td>
          <td>Beckwith, Mrs. Richard Leonard (Sallie Monypeny)</td>
          <td>female</td>
          <td>47.00</td>
          <td>1</td>
          <td>1</td>
          <td>11751</td>
          <td>52.5542</td>
          <td>D35</td>
          <td>S</td>
        </tr>
        <tr>
          <td>873</td>
          <td>0</td>
          <td>1</td>
          <td>Carlsson, Mr. Frans Olof</td>
          <td>male</td>
          <td>33.00</td>
          <td>0</td>
          <td>0</td>
          <td>695</td>
          <td>5.0000</td>
          <td>B51 B53 B55</td>
          <td>S</td>
        </tr>
        <tr>
          <td>874</td>
          <td>0</td>
          <td>3</td>
          <td>Vander Cruyssen, Mr. Victor</td>
          <td>male</td>
          <td>47.00</td>
          <td>0</td>
          <td>0</td>
          <td>345765</td>
          <td>9.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>875</td>
          <td>1</td>
          <td>2</td>
          <td>Abelson, Mrs. Samuel (Hannah Wizosky)</td>
          <td>female</td>
          <td>28.00</td>
          <td>1</td>
          <td>0</td>
          <td>P/PP 3381</td>
          <td>24.0000</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>876</td>
          <td>1</td>
          <td>3</td>
          <td>Najib, Miss. Adele Kiamie "Jane"</td>
          <td>female</td>
          <td>15.00</td>
          <td>0</td>
          <td>0</td>
          <td>2667</td>
          <td>7.2250</td>
          <td></td>
          <td>C</td>
        </tr>
        <tr>
          <td>877</td>
          <td>0</td>
          <td>3</td>
          <td>Gustafsson, Mr. Alfred Ossian</td>
          <td>male</td>
          <td>20.00</td>
          <td>0</td>
          <td>0</td>
          <td>7534</td>
          <td>9.8458</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>878</td>
          <td>0</td>
          <td>3</td>
          <td>Petroff, Mr. Nedelio</td>
          <td>male</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>349212</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>879</td>
          <td>0</td>
          <td>3</td>
          <td>Laleff, Mr. Kristo</td>
          <td>male</td>
          <td></td>
          <td>0</td>
          <td>0</td>
          <td>349217</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>880</td>
          <td>1</td>
          <td>1</td>
          <td>Potter, Mrs. Thomas Jr (Lily Alexenia Wilson)</td>
          <td>female</td>
          <td>56.00</td>
          <td>0</td>
          <td>1</td>
          <td>11767</td>
          <td>83.1583</td>
          <td>C50</td>
          <td>C</td>
        </tr>
        <tr>
          <td>881</td>
          <td>1</td>
          <td>2</td>
          <td>Shelley, Mrs. William (Imanita Parrish Hall)</td>
          <td>female</td>
          <td>25.00</td>
          <td>0</td>
          <td>1</td>
          <td>230433</td>
          <td>26.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>882</td>
          <td>0</td>
          <td>3</td>
          <td>Markun, Mr. Johann</td>
          <td>male</td>
          <td>33.00</td>
          <td>0</td>
          <td>0</td>
          <td>349257</td>
          <td>7.8958</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>883</td>
          <td>0</td>
          <td>3</td>
          <td>Dahlberg, Miss. Gerda Ulrika</td>
          <td>female</td>
          <td>22.00</td>
          <td>0</td>
          <td>0</td>
          <td>7552</td>
          <td>10.5167</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>884</td>
          <td>0</td>
          <td>2</td>
          <td>Banfield, Mr. Frederick James</td>
          <td>male</td>
          <td>28.00</td>
          <td>0</td>
          <td>0</td>
          <td>C.A./SOTON 34068</td>
          <td>10.5000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>885</td>
          <td>0</td>
          <td>3</td>
          <td>Sutehall, Mr. Henry Jr</td>
          <td>male</td>
          <td>25.00</td>
          <td>0</td>
          <td>0</td>
          <td>SOTON/OQ 392076</td>
          <td>7.0500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>886</td>
          <td>0</td>
          <td>3</td>
          <td>Rice, Mrs. William (Margaret Norton)</td>
          <td>female</td>
          <td>39.00</td>
          <td>0</td>
          <td>5</td>
          <td>382652</td>
          <td>29.1250</td>
          <td></td>
          <td>Q</td>
        </tr>
        <tr>
          <td>887</td>
          <td>0</td>
          <td>2</td>
          <td>Montvila, Rev. Juozas</td>
          <td>male</td>
          <td>27.00</td>
          <td>0</td>
          <td>0</td>
          <td>211536</td>
          <td>13.0000</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>888</td>
          <td>1</td>
          <td>1</td>
          <td>Graham, Miss. Margaret Edith</td>
          <td>female</td>
          <td>19.00</td>
          <td>0</td>
          <td>0</td>
          <td>112053</td>
          <td>30.0000</td>
          <td>B42</td>
          <td>S</td>
        </tr>
        <tr>
          <td>889</td>
          <td>0</td>
          <td>3</td>
          <td>Johnston, Miss. Catherine Helen "Carrie"</td>
          <td>female</td>
          <td></td>
          <td>1</td>
          <td>2</td>
          <td>W./C. 6607</td>
          <td>23.4500</td>
          <td></td>
          <td>S</td>
        </tr>
        <tr>
          <td>890</td>
          <td>1</td>
          <td>1</td>
          <td>Behr, Mr. Karl Howell</td>
          <td>male</td>
          <td>26.00</td>
          <td>0</td>
          <td>0</td>
          <td>111369</td>
          <td>30.0000</td>
          <td>C148</td>
          <td>C</td>
        </tr>
        <tr>
          <td>891</td>
          <td>0</td>
          <td>3</td>
          <td>Dooley, Mr. Patrick</td>
          <td>male</td>
          <td>32.00</td>
          <td>0</td>
          <td>0</td>
          <td>370376</td>
          <td>7.7500</td>
          <td></td>
          <td>Q</td>
        </tr>
      </tbody>
    </table>
</pre>

## DataFrameLoader

Pandas is an open-source data analysis and manipulation tool for the Python programming language. This library is widely used in data science, machine learning, and various fields for working with data.

LangChain's `DataFrameLoader` is a powerful utility designed to seamlessly integrate Pandas DataFrames into LangChain workflows.

```python
import pandas as pd

df = pd.read_csv("./data/titanic.csv")
```

Search the first 5 rows.

```python
df.head(n=5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
</div>



Parameters `page_content_column` (str) – Name of the column containing the page content. Defaults to “text”.



```python
from langchain_community.document_loaders import DataFrameLoader

# The Name column of the DataFrame is specified to be used as the content of each document.
loader = DataFrameLoader(df, page_content_column="Name")

docs = loader.load()

print(docs[0].page_content)

```

<pre class="custom">Braund, Mr. Owen Harris
</pre>

`Lazy Loading` for large tables. Avoid loading the entire table into memory

```python
# Lazy load records from dataframe.
for row in loader.lazy_load():
    print(row)
    break  # print only the first row
```

<pre class="custom">page_content='Braund, Mr. Owen Harris' metadata={'PassengerId': 1, 'Survived': 0, 'Pclass': 3, 'Sex': 'male', 'Age': 22.0, 'SibSp': 1, 'Parch': 0, 'Ticket': 'A/5 21171', 'Fare': 7.25, 'Cabin': nan, 'Embarked': 'S'}
</pre>
