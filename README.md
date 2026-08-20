# State Data-Center Legislation Analysis

This project collects, classifies, and visualizes state legislation related to data centers.

The workflow uses the Open States API to collect bills and Dartmouth Chat’s Qwen 3.5 model to:

1. Determine whether each bill is genuinely relevant to data centers.
2. Exclude false-positive search results.
3. Score relevant bills from 1 to 10 based on how supportive or restrictive they are toward data-center development.
4. Visualize bill supportiveness over time.

## States included

The current analysis covers:

- California
- Georgia
- Illinois
- Ohio
- Texas
- Virginia

The state list and bill-search terms can be edited in the first notebook.

## Project files

### `01_collect_openstates_bills.ipynb`

Collects candidate legislation from the Open States API.

The notebook:

- Searches the configured states using a list of data-center-related keywords.
- Searches the legislative sessions configured in the notebook.
- Combines and deduplicates search results.
- Collects bill titles, abstracts, dates, classifications, subjects, sponsors, parties, actions, votes, and source URLs.
- Treats an abstract containing fewer than 100 words as potentially insufficient.
- Attempts to retrieve the full bill text when the abstract is too short.
- Records whether full-text retrieval succeeded, failed, or requires manual review.
- Saves progress so a partially completed collection can be resumed.

Primary output:

`candidate_bills_collected.csv`

The additional legislative, sponsorship, party, action, and voting variables are retained because they may later be useful for predicting whether a bill advances.

### `02_classify_and_score_bills.ipynb`

Uses Qwen 3.5 through the Dartmouth Chat API.

The first stage determines whether each candidate bill is actually relevant to commercial or industrial data centers. A bill may be retained when it:

- Directly discusses data centers.
- Uses technology-neutral language that clearly applies to large-load facilities such as data centers.
- Establishes incentives, restrictions, utility rules, environmental requirements, permitting rules, or other policies that materially affect data centers.

Bills that only match a search keyword incidentally, or are clearly directed toward another industry, are classified as not relevant.

The second stage assigns relevant bills a supportiveness score.

Outputs:

- `candidate_bills_data_center_relevant_scored.csv`
- `candidate_bills_not_data_center_relevant.csv`

Bills classified as irrelevant are saved separately rather than permanently deleted.

### `03_visualize_bill_supportiveness.ipynb`

Creates a scatterplot of data-center bill supportiveness over time.

The plot uses:

- First legislative action date on the horizontal axis.
- Supportiveness score on the vertical axis.
- Color to distinguish states.
- A dashed line at 5 to identify the neutral score.
- Small reproducible vertical jitter to make overlapping bills visible.

Output:

`bill_supportiveness_over_time_updated.png`

The jitter only affects the visualization. It does not alter the scores in the CSV.

## Supportiveness scale

Bills are scored from 1 through 10:

| Score | Interpretation |
|---:|---|
| 1 | Prohibits data-center development or imposes a strong moratorium |
| 2 | Creates severe restrictions or major new barriers |
| 3 | Substantially discourages development |
| 4 | Creates modest net burdens or disincentives |
| 5 | Neutral, study-only, evenly mixed, or has no clear effect |
| 6 | Modestly supportive but includes meaningful conditions |
| 7 | Clearly facilitates development |
| 8 | Provides substantial incentives, infrastructure, or streamlined approval |
| 9 | Strongly promotes or subsidizes development |
| 10 | Provides exceptionally strong promotion, subsidy, preemption, or development rights |

Regulatory requirements are not automatically considered anti-data-center. The score is intended to represent the likely net effect of the legislation.

## Running the project

The notebooks are designed for Python 3.13.9 and should be run in numerical order.

### 1. Collect bills

Open `01_collect_openstates_bills.ipynb`.

Before running the collection:

- Review the state list.
- Review the search-term list.
- Enter a valid Open States API key when prompted.
- Do not place API keys directly in the notebook.

Run the notebook from top to bottom.

After collection, inspect `full_text_status` and `full_text_error`. Some bills may require manually locating the official text.

### 2. Classify and score bills

Open `02_classify_and_score_bills.ipynb`.

Enter a Dartmouth Chat API key when prompted and select the available Qwen 3.5 model.
