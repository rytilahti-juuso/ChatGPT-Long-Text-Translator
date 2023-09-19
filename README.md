# Table of Contents

- [Translator script](#translator-script)
  - [Important Notes](#important-notes)
  - [Initial set-up](#initial-set-up)
  - [Workflow](#workflow)
  - [Translator FAQ](#translator-faq)
    - [1.) How to alter chunk size? (or how to fix exceeding maximum context size -error)](#1-how-to-alter-chunk-size-or-how-to-fix-exceeding-maximum-context-size--error)
    - [2.) Why in the output.md the chunk divination is shown?](#2-why-in-the-outputmd-the-chunk-divination-is-shown)
    - [3.) Why input and output are using markdown as the file format?](#3-why-input-and-output-are-using-markdown-as-the-file-format)

# Translator script

## Translating text in latex format
ChatGPT can keep the correct syntax of latex while translating text. Please use GPT-4 as the model for this.

Important note: If the translated text compiles without errors, but the pdf preview is missing some part of the text, check that there has not appeared additional `\end{document}` in the file.


## Translating and localizing study materials

ChatGPT can also localize the translation. For example it can automatically:

- Localize names, e.g. Ossi -> Oliver.
- Localize team or company names, e.g. *TPS* -> *Arsenal*
- ChatGPT is also really good at translating code snippets (while keeping the code compilable).
  -   `Pelikortti pataseiska = new Pelikortti(Maa.PATA, 7);`
  - `PlayingCard sevenOfSpades = new PlayingCard(Suit.SPADES, 7);`.

## Important Notes:

- Due to the context size limitations of current LLMs, the python scripts broke's longer input texts internally in to multiple chunks. The text in `output.md` is combined values from these translations. 

- Currently the script gives 3 previous chunks and their translations as context for the new translation. This is to keep e.g. variable names as the same in across the whole text.

- The ChatGPT may translate the chunk correctly, but it might alter the structure of the text e.g. it might add additional headlines or modify the original quite heavily. It might hallucinate by adding additional examples. Most often these hallucinations occur on the start of the chunk. It also has a tendency to repeat headline of the previous chunk at the start of a new chunk even though the rest of the chunk has been translated correctly. Because of this it is important look the actual **content** of the translation instead of just the headlines!

- In the `output.md` also shows the chunk divination. This is because most often if a chunk translation "fails" (e.g. due to hallucination) then the whole chunk fails and the user (you) can skip directly over to the next chunk.

- Depending on the translated language, the similarity score might be 1 even with succesfull translation. If the console prints that the score is 1, check if the translation is good. With GPT-3.5 Finnish to English this problem did not occur, but with GPT-4 as the base model for translation Finnish to Swedish this did occur one time. That's why if the chosen model is GPT-4 there are no variations generated no matter the similarity score of the currently processed chunk.

- For now modifying top_p instead of temperature seems to produce better results.

- Always double check any logic (e.g. mathematical operations) that ChatGPT outputted.

- Currently the script uses model `gpt-4`.  If you decide to use `gpt-3.5-turbo-16k` or  `gpt-4-32k`, you can alter the context size to be bigger. For instructions to do that check the [FAQ](#1-how-to-alter-chunk-size-or-how-to-fix-exceeding-maximum-context-size-error) below.

- ChatGPT has tendency to (sometimes) repeat the translation of previous message instead of translating the new chunk. The provided Python script detects if this is happening and if it is, it replaces the failed translation with the original text.

- Chunks and their translations can be found from the `debug`-folder.

## Initial set-up
1. Change the API key.
2. In code line 206, select one of the prompts as the initial prompt. Currently it is set to do Finnish to English translation. IMPORTANT: write the initial prompt with the target language! (so if the translation is Finnish to Swedish, replace the initial prompt with a translated prompt written in Swedish.)
3. In the code line 228, set the chunk size to correct one.
4. Install required libraries.

## Workflow
1. Complete the initial set-up. Update selected prompt and chunk size if necessary.
2. Paste the text to the `input.md`. You don't need to worry about exceeding maximum context, the script handles that for you. The text can be in plain text, latex or in markdown format. [Here](https://gitlab.utu.fi/tech/soft/tools/edu-ai-tools/chatmd) you can find a good Python and Javascript templates on how to extract also the "syntactic sugar" from websites. The script requires some alteration if you want to extract e.g. code syntax from the website.
2. Run code in `translator_script.py`. (First run might take long time.) The console prints some of the progress when the process is running. The GPT-4 translation can take up to 2min/chunk, GPT-3.5 is quicker. For latex use always GPT-4.
3. Finally to see the translated text, open `output.md` with a viewer that supports `markdown` file format.

## Translator FAQ

#### 1.) How to alter chunk size? (or how to fix exceeding maximum context size -error)

Translation chunk size can be altered in two ways:
- Change text chunk size by altering parameter `chunk_size` in `split_into_chunks()`. This is the preferred way if the error message about **exceeding maximum token size** (context) pops up
- You can also try to modify the amount of previous chunks saved in context in `get_messages()`. However the current amount of previous chunks saved in context seems to be okay.

#### 2.) Why chunk divination can be set to be shown in the output.md?

**GPT-3.5**: The chunks are visible in the `output.md` because when the ChatGPT 3.5 is used and the translation fails (e.g. ChatGPT starts to hallucinate or the translation is set as the original text) the translation of the whole chunk fails. This way the user can skip to next chunk more easily. **However**, as stated before, ChatGPT-3.5 has tendency to hallucinate examples and additional headlines, especially in the start of a chunk, so read the chunk a little further than the headline and few starting sentences of each chunk!

**GPT-4**: With GPT-4 this debug option is almost never needed. With GPT-4 the "hallucinations" of the translation occur much more rarely than in GPT-3.5, and often are more subtle e.g. additional headline, sentence or, in latex, additional `\end{document}` appearing at the end of the chunk.

 

#### 3.) Why input and output are using markdown as the file format?
There are a couple of reasons for this.
-  It is well known format and ChatGPT probably knows the syntax of it quite well. (no need to invent the wheel again). 

- The markdown's syntax uses relatively few special characters. This means that it can keep the syntax with very low additional token usage compared other possibilities e.g. HTML format.

- Additionally, there are also various online converters that can convert other file formats (e.g. HTML) to use markdown. 