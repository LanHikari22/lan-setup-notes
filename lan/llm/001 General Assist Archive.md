
# 1 Jul 1

## 1.1 Grep via strings for binary or text

2025-07-01 Wk 27 Tue - 16:16

Quick script to either grep or grep through strings

(LLM chatgpt-4o)

```sh
find ~/parti2/Steam/steamapps/common/Expedition\ 33/Sandfall/ -type f | while read -r file; do
  if file "$file" | grep -q text; then
    # Text file, grep directly
    grep -H "your_search_term" "$file"
  else
    # Binary file, search inside strings output
    strings "$file" | grep --label="$file" -H "your_search_term"
  fi
done
```

(/LLM chatgpt-4o)

## 1.2 Quickly turn some csv file to markdown table


(LLM chatgpt-4o)
```sh
awk -F, 'BEGIN { print "| Status | File |"; print "|--------|------|"; } { printf "| %s | %s |\n", $1, $2 }' /tmp/delete/a.csv
```
(/LLM chatgpt-4o)