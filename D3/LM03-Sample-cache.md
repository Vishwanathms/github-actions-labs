```

jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Cache dependencies
        uses: actions/cache@v4
        with:
          path: ~/.npm          # same path in both jobs, preferably absolute
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
      - name: Install dependencies
        run: npm install

  job2:
    runs-on: ubuntu-latest
    needs: job1                 # ensure job1 completes first
    steps:
      - uses: actions/checkout@v4
      - name: Restore cache
        uses: actions/cache@v4
        with:
          path: ~/.npm          # same cache path
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
      - name: Use dependencies
        run: npm run build

```