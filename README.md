# iip-correcting-wordlists

This repository records the steps taken to correct the data in the Greek, Aramaic, Hebrew, and Latin masterlists used as part of the IIP's lexical analysis workflow.

## The Correction Process

- This process occurred in multiple stages. The data in Columns A, C, and E (Normalized, Lemma, and NormalizedID) was normalized as follows:
  - `(grc/arc/heb/lat)` Leading or trailing whitespace was removed.
  - `(grc/arc/heb/lat)` Any uncombined accents (e.g. the COMBINING ACUTE ACCENT Unicode character U+0301) were combined with the base letter, if possible. If the combination was not possible, the uncombined accent was stripped out.
  - `(grc/arc/heb/lat)` Any apostrophe-like character was replaced with a RIGHT SINGLE QUOTATION MARK (U+2019): \[ ’ ]. The replaced characters are: APOSTROPHE, MODIFIER LETTER APOSTROPHE, GREEK TONOS, GREEK KORONIS, GREEK PSILI, GREEK OXIA, and GREEK NUMERAL SIGN.
  - `(grc/lat)` Capitalization was handled by checking the value in Colum D (POS). If the entry was for a proper noun (PROPN), the normalization process capitalized the wordform and its lemma. Otherwise, it changed everything to lowercase.
  - `(grc)` A couple cases were found where the Latin letter ‘o’ was used in place of the Greek letter omicron (‘ο’), and so these were replaced.
  - `(grc/arc/heb/lat)` Any punctuation or other excess characters were dealt with by removing any characters that did not belong. For example, for Greek, any characters that were not part of the Greek and Coptic or the Greek Extended Unicode blocks.
  - `(grc)` Lunate sigmas were converted to regular sigmas.
  - `(grc)` Diaereses were removed.
  - `(grc)` All grave accents were converted to acute accents.
  - `(grc)` To deal with the duplicated way Unicode represents Greek acute accents (compare ‘ά’ GREEK SMALL LETTER ALPHA WITH TONOS (U+3AC) from the Greek and Coptic Unicode block with ‘ά’ GREEK SMALL LETTER ALPHA WITH OXIA (U+1F71) from the Greek Extended Unicode block), all characters using the oxia have been standardized to use the tonos instead.
  - `(heb/arc)` Pointing was removed from the Normalized and the NormalizedID columns. It was maintained in the Lemma column.
- The data in Column B (Occurrences) was standardized to remove ‘.xml’ strings and to remove duplicate wordIDs.
- After the normalization process, any rows which were exact duplicates (with the exception of their values in the Occurrences column) were merged, and their Occurrences data combined together. A temporary CSV was produced at this point.
- The wordID occurrences in this temporary CSV were checked against the words  segmented from the original XML files. The segmented words were put through the same normalization process to facilitate the comparison.
  - Any wordIDs which were found to be matches were kept.
  - Any wordIDs which did not match were saved in a separate file and are to be processed at a later point.
- An additional step was taken for Greek only: choosing a single normalized form to use in cases of variant accentuation. This concerned cases where the data in columns C onwards matched and where the data in column A differed only in accentuation. This automated process followed the following logic:
  - If a wordform contained more than one accent, it could not be selected as the normalized form.
  - The wordform with the highest count of wordID occurrences would be selected as the default form. All wordID occurrences from the other variant accentuations were added to it.
- Lastly, the orphaned wordIDs from step 4 were compared against the validated CSV. If it was found that there was only one row which they could feasibly match, they were added to that row’s occurrences list. Other orphaned wordIDs are to be handled at a later stage.
