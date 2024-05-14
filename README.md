# Úvod

- Centralizovaný (CVS, SVN) vs distribuovaný (GIT, Mercurial, Bazaar)
- ![GIT](https://imgs.xkcd.com/comics/git.png)
- Založil 2005 Linus Torvalds pro vývoj linuxového jádra	
- [Open-source](https://github.com/git/git)
- Cloud-based [Gitlab](https://about.gitlab.com), [Github](https://github.com), [Bitbucket](https://bitbucket.org), [Azure DevOps](https://azure.microsoft.com/cs-cz/products/devops), 
- On-premise [Gitlab](https://about.gitlab.com/install/), [Gerrit](https://azure.microsoft.com/cs-cz/products/devops), [Gitea](https://about.gitea.com)

__TODO__ Gitlab prozkoumat

## Principy fungování
- Každý soubor uložen jen jednou ve formě snapshotu
- Soubory jsou uložené binárně, což redukuje velikost repositáře a přenos dat
- Změny se ukládají ve formě diffu
- Všechny změny se provádí nejdřív lokálně "offline"
- Pro každou změnu se spočítá hash, změny jsou uložené ve stromové struktuře přes reference na hashe
- 4 "pracovní prostory:
    - working directory - pracovní (git add)
    - staging - připravené změny (git commit)
    - lokální repository (git push)
    - remote repository (git fetch)

## Proč používat GIT?
- Sledování změn
- Spolupráce 
- Větvení
- Zpětné vrácení změn
- Ochrana dat
- Podpora pro týmy
- Open Source a Popularita
- Flexibilita
- Rychlost
- Offline práce

## Pojmy
- Repozitář (repository / repo)
- Commit
    - Zpráva
	- Informace o autorovi
	- Unikátní reference (SHA-1 hash)
	- Parent commit (SHA-1 hash)
    - Čas commitu, změněné obsahy souborů
    - Identifikace - hash, branch, tag
- Branch (větev)
- Tag (label)

## Konfigurace
    git config --list
    git config --global user.name "Ondřej Václavek"
    git config --global user.email vaclavek@havit.cz
    git config --global core.editor "code --wait"
    git config --global merge.autoStash true

## Commit
- jedna změna -> rozdělit na logické části
- stručný a výstižný název (přítomnný čas)
- podrobný popis
- odkaz na úkol

## Status
- nezměněné soubory = working tree clean
- změněné soubory = Changes not staged for commit
- soubory ve staging = Changes to be committed
- nové soubory = Untracked files

## .gitignore
- [šablona pro různé jazyky](https://github.com/github/gitignore/tree/main)
- ignorování commitu adresářů nebo souborů

## fetch
Stáhne změny ze vzdáleného repositáře bez toho, aby se změny aplikovaly do indexu

    git fetch

## pull
Stáhne fetch ze vždáleného repositáře a aplikuje je do indexu.
Je ekvivalentní jako git fetch && git merge

    git pull 

## log
pro výpis všech commitů, zobrazení změn v souborech, filtrování historie a zobrazení historie větví

    git log --oneline # výpis na jeden řádek
    git log -3 # jen 3 poslední commity
    git log --oneline --after="2024-05-01" --before="2024-05-08" # filtrování
    git log --all # všechny větve
    git log --oneline --all --decorate --graph # zobrazení grafu branchí a commitů

## reset
Umožňuje vrátit obsah repositáře k určitému commitu. Změny __po daném commitu__ budou odstraněny.

    git reset --soft commitid # pouze změní HEAD
    git reset --mixed # zachová změněné soubory, odstraní commity
    git reset --hard # odstraní změny

## branch
Větve jsou pouze ukazatele na commit. Když vytvoříme větev, git vytvoří nový ukazatel, nemění historii.

Typické uspořádání:
- Hlavní větev - main (master)
- Feature větve - vývoj nových funkcí
- Hotfix větve - opravy chyb

Výhody:
- Paralelní vývoj
- Izolace změn
- Experimenty bez vlivu na zbytek codebase
- Správa verzí

Práce s branchemi:

    git branch --all # seznam všech větví
    git branch newbranch # vytvoření nové větve
    git checkout newbranch # přepnutí na branch
    git checkout master # přepnutí na brach master
    git branch -m newbranch newbranch2 # přejmenování
    git branch -d newbranch2 # smazání

## merge
Slouží k integraci změn z jedné větve do druhé, čímž se spojí historie obou větví.
Provede nový "merge" commit (kromě fast-forward varianty)

    git merge newbranch

V případě konfliktu je merge přerušen, pokračování je možné

    git merge --continue # pokračování v commitu
    git merge --abort # zrušení commitu

Typy sloučení:
- fast-forward merge - novější commity se "napojí" na poslední commit. Nevyžaduje lidský zásah, nevzniknou konflikty. Nelze použít u větví, kde probíhaly změny
- 3-way merge - sloučení změn z větví, kde v obou probíhaly změny

Při sloučení mohly vzniknout konflikty, pokud došlo k:
- úpravě stejného řádku souboru
- mazání nebo přejmenování změněných souborů
- změny v různých částech stejného souboru

Řešení konfliktů je běžnou součástí práce, pokud probíhá paralelní vývoj nad stejnou codebase.
- Pokud GIT nedokáže sám vyřešit kolizi, označí konflikt a nechá na uživateli, aby jej vyřešil. 
- Merge probíhá, dokud nejsou všechny konflikty vyřešené.
- Pokud uživatel neví, jak konflikty dokončit, může merge zrušit
- GUI významně pomáhá k řešení konfliktů - umožňuje vizuálně porovnat konfliktní změny

### Řešení konfliktu

Příklad (včetně vytvoření konfliktu)
    git branch new # založení nové branche
    echo "New" >> new.txt # zapsání textu do souboru
    git add new.txt # přidání souboru do gitu
    git commit -m "Added in new branch" # commit
    git checkout master # přepnutí do master branche
    echo "Master" >> new.txt # zapsání textu do souboru (obsah je konfliktní)
    git commit -m "Added in master" # commit
    git merge new # pokus o merge branche new do branche master
    git status # stav gitu
    cat new.txt # podíváme se do souboru
    notepad new.txt # opravíme konflikt
    git add new.txt # přidáme změněný soubor do gitu
    git commit -m "Merged" # commit

## diff

Zobrazí změny mezi commity / branchemi 

    git diff newbranch master

## Aplikace
- [GitHub Desktop](https://desktop.github.com/)
- [Sourcetree](https://www.sourcetreeapp.com/)
- [Visual Studio Code](https://code.visualstudio.com/)
- [GitKraken](https://www.gitkraken.com/)
- [Visual Studio](https://visualstudio.microsoft.com/)

## cheatsheat

![GIT příkazy](img/git-prikazy.svg)