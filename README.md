# Markdown Processor and Embedder

md-embed processes markdown files, cleans and prepares the data, splits the text into manageable chunks, and creates embeddings for use in vector databases (specifically ChromaDB). It supports multiple input methods and provides options for customizing the splitting and embedding process.<br />

## Features

*   **Multiple Input Methods:**<br />
    *   JSON file containing URLs and markdown data<br />
    *   Folder of markdown files<br />
    *   Single markdown file<br />
*   **Data Cleaning:**<br />
    *   Removes duplicate entries based on URL section titles<br />
    *   Handles encoding issues<br />
    *   Sanitizes filenames for safe saving<br />
    *   Optionally filters out files containing "404" and "page not found" (can be disabled)<br />
    *   Removes lines containing the copyright symbol "©"<br />
*   **Text Splitting:**<br />
    *   **Markdown Header Splitting:** Splits text based on specified markdown header levels (e.g., `#`, `##`).  Allows for custom header level selection. Preserves header hierarchy in metadata<br />
    *   **Recursive Character Text Splitting:** Splits text into chunks of specified size and overlap<br />
    *   **Link Removal:**  Optionally removes markdown links, keeping only the link text<br />
*   **Embedding Generation:*<br />
    *   Supports **Hugging Face** embeddings (using `langchain_huggingface`). Defaults to `all-MiniLM-L6-v2`<br />
    *   Supports **Ollama** embeddings (using `langchain_community`). Defaults to `nomic-embed-text`, requires a local Ollama server running at `http://localhost:11434`<br />
*   **Vector Database Integration:**<br />
    *   Uses **ChromaDB** (`langchain_chroma`) to store embeddings and associated metadata<br />
    *   Allows specifying the collection name and persistence directory<br />
    *   Handles large datasets by processing in batches<br />
* **Logging:**<br />
    * Comprehensive logging through the `logging` module<br />
* **Duplicate Logs**:<br />
    * Writes URLs with duplicate sections to a log<br />
* **Removed Files Logs**<br />
    * Write to a log files that have been removed due to filters<br />

## Requirements

*   Python 3.7+<br />
*   `langchain` (various components - see import statements)<br />
*   `chromadb`<br />
*   `tqdm`<br />
*   `beautifulsoup4` (if you were scraping, but this script doesn't actually use it)<br />
*  `requests` (if you were scraping, but this script doesn't actually use it)<br />

To install the required packages, run:<br />

```bash
pip install langchain langchain-chroma langchain-huggingface tqdm
```
```markdown
If you are planning to use Ollama, you need to:
Install Ollama by following the instructions provided at Ollama's official website.
Run an Ollama server locally on port 11434
```
md-embed can be run from the command line. It provides a command-line interface using argparse with the following option:<br />
--filters-off: Disables the "404" and "©" filters<br />
The script will then guide you through a series of interactive prompts to configure the processing:<br />
Input Method Selection: Choose between JSON input, a folder of markdown files, or a single markdown file<br />
Input File/Folder/URL: Provide the path to the input file or folder, as appropriate<br />
Output Folder (for JSON input): Specify the directory where cleaned markdown files will be saved<br />
Data Cleaning Options: The script will show total entires and total duplicates<br />
Language: Specify the primary language of the input files (e.g., "TypeScript", "Python")<br />
Splitting Method: Choose between "markdown" (header-based splitting) and "recursive" (chunk size and overlap)<br />
Markdown Splitting Options (if applicable):<br />
Remove Links: Choose whether to remove markdown links<br />
Header Levels: Specify which header levels to split on (e.g., "1,2,3" for #, ##, and ###). Enter "all" for all header levels<br />
Recursive Splitting Options (if applicable):<br />
Remove Links: Choose whether to remove markdown links<br />
Chunk Size: Specify the desired chunk size (in characters)<br />
Chunk Overlap: Specify the desired chunk overlap (in characters)<br />
Preview Splits: Choose whether to preview the split data ("yes", "full", or "no")<br />
Split Again: You'll be prompted to continue or modify the settings<br />
Embedding Method: Choose between "huggingface" and "ollama"<br />
Embedding Model (Hugging Face): Enter the Hugging Face model name (defaults to all-MiniLM-L6-v2)<br />
Embedding Model (Ollama): Enter the Ollama model name (defaults to nomic-embed-text)<br />
Persistence Directory: Specify the directory where the ChromaDB database will be stored<br />
Collection Name: Choose a name for the ChromaDB collection<br />
Example (JSON Input):<br />
```bash
python md-embed.py
```
Follow the prompts, providing the necessary information (input file, output folder, embedding choices, etc.)<br />
Example (Disabling Filters):
```bash
python md-embed.py --filters-off
```
Cleaned Markdown Files (JSON Input): If using JSON input, the script will save cleaned markdown files to the specified output folder<br />
ChromaDB Database: The script will create a ChromaDB database in the specified persistence directory, containing the embeddings and metadata<br />
Logs: The logs directory will contain logs of removed files (if any) and duplicate entries (if using JSON input)<br />
file_to_url.json: Json file that contains the original URL of each document<br />
Error Handling<br />
The script includes error handling for various scenarios, such as:<br />
Invalid input file/folder paths<br />
File I/O errors<br />
Exceptions during data cleaning, splitting, or embedding<br />
Invalid user input for prompts<br />
Errors are logged using the logging module<br />
Notes<br />
The script assumes that the input JSON data has "url" and "markdown" keys for each entry<br />
The script uses uuid4 to generate unique IDs for each document in the vector database<br />
The script processes in batches to deal with a large number of splits<br />
