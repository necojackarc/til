# Grep with Multiple Conditions

Sometimes, you want to grep lines that contains any of the given strings.

Let's say you have `contains.txt` and `source.csv`:

```txt
apple
banana
orange
```

```csv
item,price
apple,£1
pen,£1
pineapple,£3
orange,£0.4
racket,£200
ball,£3
```

You can get the results by using `for-in` on Bash:

```bash
for LINE in $(cat ./conditions.txt); do grep "$LINE" ./source.csv; done
```

which returns:

```csv
apple,£1
pineapple,£3
orange,£0.4
```

