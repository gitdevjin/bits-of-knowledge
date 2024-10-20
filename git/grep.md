### How to search a piece of code in repository

In the root directory of your project enter the following.

```bash
git grep --heading -n -C 2 CtrlCmd
```

`--heading`: Provide the file name as a heading.
`-n`: Provide the line numbers of the code.
`-C 2`: Provide 2 extra context code lines
`CtrlCmd`: the code that you are searching.
`-- *.ts`: Search only in files that match `*.ts` 