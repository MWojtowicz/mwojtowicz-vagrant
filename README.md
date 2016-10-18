# Witaj!

## Katalogi

Stwórz sobie katalog, w którym będziesz przechowywać pliki związane z tym
kursem, np. w folderze użytkownika utwórz folder
`KURS`. Następnie wewnątrz tego folderu sklonuj to repozytorium
w którym się aktualnie znajdujesz,
oraz przynajmniej jedno z moich repozytoriów kursowych.

Aby rozpocząć pracę z kursem, musisz mieć zainstalowane oprogramowanie:

### Konfiguracja mniej wymagająca

1. GIT - system zarządzania wersji (https://git-scm.com/downloads)
2. Oracle VirtualBox (https://www.virtualbox.org/wiki/Downloads)
3. Vagrant (https://www.vagrantup.com/downloads.html)

(na dzień 18 października 2016 Vagrant wspiera maksymalnie wersję 5.1.x programu VirtualBox)

a następnie w folderze w którym chcesz pracować, wpisz w konsoli polecenia:

```
git clone git@github.com:MWojtowicz/mwojtowicz-vagrant.git
cd mwojtowicz-vagrant
vagrant up
```

Jeśli nie chcesz instalować oprogramowania z listy powyżej,
potrzebujesz mieć zainstalowane i skonfigurowane na własną rękę:

### Konfiguracja bardziej wymagająca

1. GIT
2. PHP
3. Serwer HTTP (np. Apache, Nginx)
4. Baza danych (np. MySQL)
5. Composer (https://getcomposer.org/)
6. Symfony installer (http://symfony.com/download <- kliknij w "install the Symfony installer") 