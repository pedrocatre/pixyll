My readme for things I might need fast.

### Jekyll Serve

Then, start the Jekyll Server. I always like to give the `--watch` option so it updates the generated HTML when I make changes.

```
$ jekyll serve --watch
```

If I get the issue:

conversion error: Jekyll::Converters::Scss encountered an error while converting 'css/pixyll.scss':
Invalid US-ASCII character "\xC2" on line 7
                    
I can do this: https://github.com/johnotander/pixyll/issues/193:
run this in the terminal

```
if locale -a | grep -qF 'C.UTF-8'; then
    loc="C.UTF-8"
else
    loc="en_US.UTF-8"
fi
export LANG=$loc
export LC_ALL=$loc
```