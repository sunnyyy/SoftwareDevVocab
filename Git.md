Git Tutorial

```bash
mkdir BookList			# create a folder called "BookList"
cd BookList				# navigate to your new folder
git init				# initialize git, aka turn this folder into a repo
vim bookList.md
```

```bash
git status				# see that there has been a change in the file
git add *				# add all the new changes to then commit
git commit -m "this is a new list of books"
git log					# see the commit listed in the logs
git push				# push it online to the server
```

Get URL of server

```bash
git push -u <server>	# NOW push it online to the server
```

Add all HP books

```bash
git status				# see that there has been a change in the file
git diff				# see which changes you've made
git add bookList.md		# add the changes you made to this file
git commit
```

```
[TASK-1] Add Harry Potter series

[Problem] HP series was missing from the list of books

[Solution] Added all 7 books from the HP series

[Test] Not necessary, just a change to a `.md` file

[CR] codeReview.url.com/7777777
```

```bash
git log 				# scroll through the detailed list of commits
git log --oneline		# get the shortened list of commits
git push
```

Change "sorcerer" to "philosopher"

```bash
git status				# see that 1 file has changed
git diff				# verify your changes
git add *				# add all files with changes
git commit
```

```
[TASK-2] Use British titles for Harry Potter books

[Problem] What is this American rubbish

[Solution] Fixed the title for HP 1 to be "philosopher" instead of "sorcerer"

[Test] Not necessary

[CR] codeReview.url.com/7777778
```

```bash
git push				# push "philosopher" changes upstream
```

Keysmash

```bash
git checkout -- bookList.md
git tag "HPBooksComplete"
```

Let's say you had these two different tasks in your backlog:
- Add odd-numbered books from the series _A Song of Ice and Fire_ (better known by its TV show name, _Game of Thrones_)
- Add even-numbered books from the series _A Song of Ice and Fire_

```bash
git branch
git checkout -b oddNums		# this branch will be used for odd-numbered GoT books
git checkout -b evenNums	# this branch will be used for even-numbered GoT books
```

// Add list of books one by one

```bash
git merge				# try to merge, but notice that there's a bit of a mess
git rebase				# try to rebase, but also notice that it's a bit messy
git rebase -i 			# better
```

However, let's now say that we've decided not just to hold _lists_ of books, but _reviews_ of books. In this case, we might be concerned that our `bookList.md` file is going to become too long, in which case we should split it into two different files -- one per series.

Split the files manually.

Now we want to delete our original list. This is what would happen if we simply did `rm bookList.md`:



The solution is to use the git version:

```bash
git rm bookList.md
```

Things we didn't cover:
- git reset
- git reset HEAD{1}
- git commit -b "testing"