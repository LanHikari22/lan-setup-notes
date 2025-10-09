# 1 Objective

\#lan #llm #editing

Sometimes we have a random string and we would like it tabulated according to a marker, like "@"

# 2 Solution

You can save the content to a file, like

````
LexerRecord { @lexon_type: LexonType::ThumbFuncStart,    @lexon_data: LexonData::Sign, @capture: "thumb_func_start ".to_string() },
LexerRecord { @lexon_type: LexonType::Ident,             @lexon_data: LexonData::Word("call_m4aSoundMain".to_string()), @capture: "call_m4aSoundMain\n".to_string() },
LexerRecord { @lexon_type: LexonType::ColonLabel,        @lexon_data: LexonData::Word("call_m4aSoundMain".to_string()), @capture: "call_m4aSoundMain:\n    ".to_string() },
LexerRecord { @lexon_type: LexonType::ThumbOpcode,       @lexon_data: LexonData::Word("push".to_string()), @capture: "push ".to_string() },
LexerRecord { @lexon_type: LexonType::LCurly,            @lexon_data: LexonData::Sign, @capture: "{".to_string() },
````

Then use

````sh
cat a | column -s '@' -t

# out
LexerRecord {   lexon_type: LexonType::ThumbFuncStart,      lexon_data: LexonData::Sign,                                    capture: "thumb_func_start ".to_string() },
LexerRecord {   lexon_type: LexonType::Ident,               lexon_data: LexonData::Word("call_m4aSoundMain".to_string()),   capture: "call_m4aSoundMain\n".to_string() },
LexerRecord {   lexon_type: LexonType::ColonLabel,          lexon_data: LexonData::Word("call_m4aSoundMain".to_string()),   capture: "call_m4aSoundMain:\n    ".to_string() },
LexerRecord {   lexon_type: LexonType::ThumbOpcode,         lexon_data: LexonData::Word("push".to_string()),                capture: "push ".to_string() },
LexerRecord {   lexon_type: LexonType::LCurly,              lexon_data: LexonData::Sign,                                    capture: "{".to_string() },

````

Unlike the aligning which is tedious, placement of marks like @ could be easier, such as `s/capture/@capture/g`.

# 3 LLM Instructions

* This is a diagnostic document and not a conversation. Everything shared is context. Address the questions tagged (Q#) like (Q1) for example. If you see something like (~1), assume it part of the archive and not a latest set of questions.
  * Since it keeps occurring, I ask Again
  * !!! NEVER RESPOND TO (~1), (~2), etc.
  * ONLY respond to the tagged questions. Nothing else.

(LLM chatgpt-4o)
(Note: Between these tags is responses from ChatGPT-4o.
(/LLM chatgpt-4o)

(LLM chatgpt-4o)
(/LLM chatgpt-4o)

# 4 Journal

2025-07-20 Wk 29 Sun - 02:08

(LLM chatgpt-4o)

````sh
echo -e "name|age|city\nAlice|30|NY\nBob|25|LA" | column -s '|' -t
````

(/LLM chatgpt-4o)

# 5 References
