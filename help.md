# How to make `master` have one single commit with `README.md` and `develop` be above master 

gh-pages references to c2499f407 commit - OK

    ~/work/iba/css-html-into (gh-pages)$ git log
    commit c2499f407e5ae3888be9ca05ff3efd2459754e8a (HEAD -> gh-pages, origin/gh-pages, origin/develop, develop)
    Author: Kirill Zayats <kirill.zayats77@gmail.com>
    Date:   Mon May 4 12:53:51 2020 +0300
    
        refactor code 'markup fixed 2


develop references to c2499f407 commit - OK

    ~/work/iba/css-html-into (master)$ git checkout develop 
    Branch 'develop' set up to track remote branch 'develop' from 'origin'.
    Switched to a new branch 'develop'
    ~/work/iba/css-html-into (develop)$ git log
    commit c2499f407e5ae3888be9ca05ff3efd2459754e8a (HEAD -> develop, origin/gh-pages, origin/develop)
    Author: Kirill Zayats <kirill.zayats77@gmail.com>
    Date:   Mon May 4 12:53:51 2020 +0300
    
        refactor code 'markup fixed 2


first commit of `develop`  (used git log command):

    commit 164f0086af4d6a6966c505c8d88f34dc3d6b4e52
    Author: Kirill Zayats <kirill.zayats77@gmail.com>
    Date:   Mon May 4 12:11:13 2020 +0300
    
        add page without adaptability


`master` is above `develop` - it is **WRONG**: 

    ~/work/iba/css-html-into (master)$ git log
    commit 8d1bb85c1377221f16c49e3bce6464f71a1053c5 (HEAD -> master, origin/master, origin/HEAD)
    Merge: 164f008 c2499f4
    Author: KirillZayats <55273299+KirillZayats@users.noreply.github.com>
    Date:   Mon May 4 13:22:08 2020 +0300

    Merge pull request #1 from KirillZayats/develop
    
    Create web-site

    commit c2499f407e5ae3888be9ca05ff3efd2459754e8a (origin/gh-pages, origin/develop, gh-pages, develop)
    Author: Kirill Zayats <kirill.zayats77@gmail.com>
    Date:   Mon May 4 12:53:51 2020 +0300
    
        refactor code 'markup fixed 2
 
 show all commits in a one-line way:  

    ~/work/iba/css-html-into (master)$ git log --pretty=oneline
    8d1bb85c1377221f16c49e3bce6464f71a1053c5 (HEAD -> master, origin/master, origin/HEAD) Merge pull request #1 from KirillZayats/develop
    c2499f407e5ae3888be9ca05ff3efd2459754e8a (origin/gh-pages, origin/develop, gh-pages, develop) refactor code 'markup fixed 2
    a613765f5a29b4fab8d9d7e674cc6eb668ae1b11 refactor code 'markup fixed
    701bf16078cc2ebd0fd5eb2c52c796ef450f17cb Create README.md
    3680702f777bc8d214552c1943762df96260bc6b refactor code: delete unnecessary and double-checked the markup
    12671fe4de9e44274b33ea82d2145651f6b17079 add adaptivity for tablet
    f6cc92e297dcbb23888e857c593699e8ecd6f14d add adaptivity for phone
    dc96128552aa5b8f1e846ec1f8dbad00608d3f87 add adaptability for PC
    164f0086af4d6a6966c505c8d88f34dc3d6b4e52 add page without adaptability

the first commit for master is: 164f0086af - not OK
analysing commits:

    ~/work/iba/css-html-into (master)$ git show  --pretty="" --name-only 701bf16078cc2ebd0fd5eb2c52c796ef450f17cb
    README.md
    ~/work/iba/css-html-into (master)$ git show  --pretty="" --name-only 164f0086af
    Task1.html
    style.css

it seems the first commit already has html and css files and 701bf16078 commit had README.md file as was required. Solution: rebase commits tree and make 701bf16078 the first commit for all branches. to do that you need to execute following command:

    $ git rebase -i --root

then rebase file will be opened in your default text editor. you will see something like:

    pick 164f008 add page without adaptability
    pick dc96128 add adaptability for PC
    pick f6cc92e add adaptivity for phone
    pick 12671fe add adaptivity for tablet
    pick 3680702 refactor code: delete unnecessary and double-checked the markup
    pick 701bf16 Create README.md
    pick a613765 refactor code 'markup fixed
    pick c2499f4 refactor code 'markup fixed 2

remove all commits except 701bf16. + save and close file:

    pick 701bf16 Create README.md

now you can see there is only one commit in `master` branch:

    ~/work/iba/css-html-into (master)$ git log
    commit 6ae750ef05a4afe618c99a7b0cd32227244472cd (HEAD -> master)
    Author: Kirill Zayats <kirill.zayats77@gmail.com>
    Date:   Mon May 4 12:35:55 2020 +0300
    
        Create README.md

the next step to do is rebase `develop` branch using `master` branch:

    $ git rebase -i master develop

Now you can see that `master` branch points to the first commit in a tree and the `develop` branch points to the last commit. That is what we needed. 

    ~/work/iba/css-html-into (develop)$ git log --pretty=oneline
    559e88b1337dbbd4ac15c3adfbaa20c4daa6836d (HEAD -> develop) refactor code 'markup fixed 2
    82258af67909a8c27e956b712a277362ac30b216 refactor code 'markup fixed
    60011aad7a5ed824e8e766d0ebc7a0a8989a2823 refactor code: delete unnecessary and double-checked the markup
    c5bfb58aa91b12c7e8addd664c5163fda80b9fb2 add adaptivity for tablet
    78ac2a9abe5905b4bf8aa8a53282f1f8e5c00b9d add adaptivity for phone
    09ffda6e8e7223818734a8de5790e01e207796dc add adaptability for PC
    69469194f117c76dfb38ef1c94d86dca19d18a71 add page without adaptability
    6ae750ef05a4afe618c99a7b0cd32227244472cd (master) Create README.md

Now you can upload the changes in remote repository. You will need --force flag because you updated the commit history:

    $ git push --force origin master
    $ git push --force origin develop
