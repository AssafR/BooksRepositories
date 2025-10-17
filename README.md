"# BooksRepositories" 


Add each “book” repo as a submodule (pick any folder layout you like):

# Example: track the 'main' branch, shallow clone to save space
git submodule add --branch main --depth 1 https://github.com/author/book1.git repos/book1
git submodule add --branch master --depth 1 https://github.com/author2/book2.git repos/book2
git commit -m "Add book submodules"
git push


That writes a .gitmodules file which is your index of links.

Tip: If Git complains about --depth, you can set shallow per-submodule:
git config -f .gitmodules submodule.repos/book1.shallow true
git config -f .gitmodules submodule.repos/book2.shallow true
git add .gitmodules
git commit -m "Make submodules shallow"


Update all books later (the usual workflow)

Update all books later (the usual workflow)

git pull
git submodule update --init --recursive --remote --depth 1
git add .
git commit -m "Update books"
git push


--remote moves each submodule to the latest commit of its tracked branch (the one you set with --branch).

Using --depth 1 keeps them light.


New machine? One command


New machine? One command

git clone --recurse-submodules https://github.com/<you>/<books-tracker>.git
# If you forgot --recurse:
git submodule update --init --recursive


Bulk add (PowerShell helper)
https://github.com/author/book1.git repos/book1 main
https://github.com/author2/book2.git repos/book2 master


Then run:

Get-Content .\repos.txt | ForEach-Object {
  $parts = $_ -split '\s+'
  $url=$parts[0]; $path=$parts[1]; $branch=$parts[2]
  git submodule add --branch $branch --depth 1 $url $path
}
git commit -m "Add submodules from list"
git push


