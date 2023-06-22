This project focuses on the analysis of digital evidence, such as files stored on a hard drive, data from web servers or communication between users. The aim of the project is to recreate the chronological sequence of actions of a person using a computer using available open-source tools:
- autopsy
- volatility
- bulk_extractor
- recoverjpg
- Rescure Pro



# Własny pomysł 

Jako iż w pewnej części zajmuję się fotografią i filmowaniem, sprawdzę jak efektywne jest 



1. odzyskiwanie zdjęć z profesjonalnych kart SD Sandiska za pomocą dostępnych narzędzi open source (**foremost**) jak i komercyjnych - **RescuePRO Deluxe**
2. jak bezpieczne jest formatowanie kart za pomocą oprogramowania aparatu


## Przygotowanie środowiska






1. Stworzenie kopii binarnej karty SD, która była w połowie zapełniona zdjęciami i filmami - karta podłączona do komputera za pomocą adaptera






2. Następnie tworzę kopię binarną pamięci aparatu - który jest podłączony do komputera za pomocą kabla micro USB - w taki sposób chcę sprawdzić, czy jest jakakolwiek różnica w zawartości otwartej pamięci




Spostrzeżenie: na pierwszy rzut oka, po podłączeniu aparatu do komputera zawartość karty SD się nie zmieniła - jednakże znacząco spadła prędkość odczytu, ciężko jest odtworzyć jakikolwiek film w sposób _bez zobaczenia artefaktów_. Podczas tworzenia kopii binarnej również znacząco spadła prędkość - z 60/160 MB/sec do około 11 MB/sec

Niestety w trakcie pracy programu nastąpił błąd

Okazuje się, że jeśli aparat jest podłączony do komputera za pomocą portu USB, to po pewnym czasie system samoistnie odłącza to urządzenie (prawdopodobnie tworzenie obrazu karty trwało zbyt długo z uwagi na ograniczoną prędkość odczytu)

W takim razie analiza powyższego podpunktu przyjmie trochę inną formę - **ile danych można uzyskać z uszkodzonej karty/uszkodzonego obrazu?**



3. Formatuję kartę za pomocą oprogramowania aparatu (DMC gx80 Ver. 1.3), a nastepnie tworzę jej kopie binarną

    



    


4. Nadpisuję wcześniej sformatowaną kartę innymi zdjęciami (naturalny proces w pracy fotografa) a następnie robię ostatnią już kopię binarna


## Analiza


### Foremost

z doświadczenia mogę stwierdzić, że jest to naprawdę skuteczne narzędzie. Problemem jest dostępność rozszerzeń, a raczej ich brak

Formaty, które będą mnie interesowały to: 



* .JPG
* .RW2 (domyślne rozszerzenie do zapisywania surowych zdjęć firmy Panasonic)
* .MOV

Następnie w pliku konfiguracyjnym dodaję rozszerzenia - .rw2 oraz .mov. Warto tutaj wspomnieć, że aby to zrobić należy znaleźć tak zwany _header_ rozszerzenia. Można to zrobić za pomocą analizowania kolejnych plików komendą _hexdump -C_ oraz porównywania części wspólnych




Zauważyłem, że w wszystkich plikach z rozszerzeniem .rw2 pojawia się powyższe znaki

Plik _/etc/foremost.conf_

Uruchamiam program foremost na każdym z plików




Powstały 4 osobne foldery wraz z wyekstraktowanymi zdjęciami, w każdym znajdują się jakieś odzyskane zdjęcia, które podzielę na następujące kategorie:






1. W pełni odzyskane zdjęcia, w maksymalnej rozdzielczości, rozmiarze oraz zachowaną resztą  - dane exif. Bez problemu można je przeglądać a nawet dalej użyć
2. Zdjęcia z nie zachowaną pełną rozdzielczością, jednakże z zachowaną całą resztą (w tym metadane). Również bezproblemowe jeśli chodzi o ich sam podgląd
3. Obrócone zdjęcia z możliwością podglądu, w zmniejszonej rozdzielczości (w tym dziwnych proporcjach), bez zachowanych metadanych (punkt 2) jednakże bez metadanych)
4. Uszkodzone zdjęcia - w niektórych nawet pozostały metadane
5. Podglądy miniaturek filmów - w postaci zdjęc w formacie .jpg z naprawdę małą rozdzielczością (416 × 240 pixeli) i brakiem jakichkolwiek metadanych


### 


Moja hipoteza a propos punktu **2)** - mimo próby zidentyfikowania charakterystycznych headerów surowych plików z rozszerzeniem .**rw2**, nie udało się żadnego odzyskać w pierwotnej formie, jednakże powyższe zdjęcia o zmniejszonej rozdzielczości mogą być z nich odzyskane. Niestety nie zawsze skutecznie - stąd powstaje problem obracania zdjęcia z uwagi na brak metadanych (jak punkt **3)**). Co ciekawe, takie zachowanie wydaje się być częściowo normalne, z mojego  doświadczenia - próbując przeglądać surowe zdjęcia prosto z karty SD, bez odpowiedniego kodeka, Windows ma problemy z odtwarzaniem takich plików, niepotrzebnie je obraca i nie wyświetla żadnych metadanych.

Los zdjęć typu **4)** to najprawdopodobniej następująca sytuacja: zdjęcie zostało usunięte w trakcie sesji a następnie nadpisane przez inne - dlatego pozostała część danych.

Dostępne zdjęcia w folderach typu _output_ najczęściej zostały odzyskane w następujących kombinacjach:



* 1) i 2)
* 1), 2) i 3)
* 1) i 3)
* 2) i 3)
* 2)
* 5)
* 4)

Spostrzeżenia:



* Zdjęcia o zmniejszonej rozdzielczości zachowują dokładne proporcje wymiaru pixeli, oraz mają 10 krotnie mniejszą rozdzielczość
* Po głębszej analizie, zauważyłem, że podczas sesji gdzie zapisywałem same zdjęcia w formacie RAW, można odzyskać **1)** i **2)** lub **3) **- w takim razie zdjęcia o zmniejszonej rozdzielczości mogą być zarówno miniaturkami plików .**jpg** jak i **.rw2 **- warto zaznaczyć iż nie jest to reguła** **
* sposób oraz warunki utworzenia zrzutu obrazu karty SD nie mają wpływu na powyższe własności (kategorie i kombinacje)

Terminologia, której będę używał w następującej analizie:



* _sandisk_sd_ - odzyskane pliki z kopii binarnej karty SD, która była w połowie zapełniona zdjęciami i filmami - karta podłączona do komputera za pomocą adaptera
* _sandisk_sd_format_ - odzyskane pliki z kopii binarnej karty SD, która była sformatowana za pomocą oprogramowania aparatu
* _lumix_with_sd_ - odzyskane pliki z kopii binarnej pamięci aparatu - który jest podłączony do komputera za pomocą kabla micro USB
* _sandisk_sd_format_overwritten_ - odzyskane pliki z kopii binarnej karty SD najpierw sformatowanej za pomocą oprogramowania aparatu, a następnie w połowie nadpisana innymi plikami (zdjęciami)

<table>
  <tr>
   <td>
własność
   </td>
   <td>sandisk_sd
   </td>
   <td>sandisk_sd_format
   </td>
   <td>lumix_with_sd
   </td>
   <td>sandisk_sd_format_overwritten
   </td>
  </tr>
  <tr>
   <td>Ilość odzyskanych plików
   </td>
   <td>1295
   </td>
   <td>1294
   </td>
   <td>741
   </td>
   <td>1360
   </td>
  </tr>
  <tr>
   <td>występujące kombinacje
   </td>
   <td>wszystkie
   </td>
   <td>wszystkie
   </td>
   <td>wszystkie
   </td>
   <td>wszystkie
   </td>
  </tr>
  <tr>
   <td>Ilość odzyskanych zdjęć w wysokiej rozdzielczości
   </td>
   <td>400
   </td>
   <td>399
   </td>
   <td>242
   </td>
   <td>447
   </td>
  </tr>
  <tr>
   <td>Ilość odzyskanych zdjęć wraz z dużą ilością metadanych
   </td>
   <td>800
   </td>
   <td>799
   </td>
   <td>483
   </td>
   <td>839
   </td>
  </tr>
  <tr>
   <td>Ilość odzyskanych zdjęć bez metadanych
   </td>
   <td>494
   </td>
   <td>494
   </td>
   <td>257
   </td>
   <td> 520
   </td>
  </tr>
  <tr>
   <td>Ilość odzyskanych miniatur filmów
   </td>
   <td>85
   </td>
   <td>85
   </td>
   <td>12
   </td>
   <td>70
   </td>
  </tr>
</table>


Na potrzeby powyższych statystyk napisałem proste skrypty:;







Co ciekawe, analizując dane exif, znalazłem model obiektywu, którego nie posiadam, a mimo to dzięki niemu zostało wykonane zdjęcie




Nie zgadza się również przesłona - zdjęcie zostało wykonane z wartością równa f/1,2 a minimalna dla domniemanego obiektywu to f/3,5


### RescuePro Deluxe

Na sam początek warto zaznaczyć iż program współpracuje z bezpośrednio zamontowanymi nośnikami lub zrzutami obrazu z rozszerzeniem _.img_

Dlatego muszę skonwertować wcześniejszy obraz kart z rozszerzenia _.bin_ do _.img_

Rozpoczynam skanowanie:




Wybieram opcję Recover All Files




Zrzut procesu działania programu




Przykładowy wygląd programu zaraz po zakończeniu skanowania




Warto zaznaczyć, że proces skanowania programu trwał zauważalnie krócej niż w przypadku foremost

Już na pierwszy rzut oka można zobaczyć jeden z największych plusów tego programu - ilość dostępnych rozszerzeń. \
W tym przypadku udało się odzyskać surowe zdjęcia w formacie **.rw2** oraz filmy **.mp4** - czego się nie udało dla oprogramowania **foremost**, mimo próby przeanalizowania i dodania nagłówka do reguł

Już na pierwszy rzut oka widać, że zostało odzyskanych znacznie więcej zdjęć -  w tym wiele w wysokiej rozdzielczości i z metadanymi - których nie udało się odzyskać foremost’em

Co najważniejsze, działa odtwarzanie plików wideo - nawet w oryginalnej rozdzielczości oraz pełną ścieżką audio (fullhd a nawet 4k), jednakże mam wrażenie, że w pomniejszonej jakości

Odzyskane surowe zdjęcia są pełnoprawnymi plikami nadającymi się do dalszego użytku - np. obróbki graficznej

Program RescuePro również odnalazł uszkodzone pliki, dokładnie te same co foremost.


#### Porównanie ilości odzyskanych poszczególnych plików - RescuePro vs Foremost

Na czerwono dane z Foremost


<table>
  <tr>
   <td>własność
   </td>
   <td>sandisk_sd
   </td>
   <td>sandisk_sd_format
   </td>
   <td>lumix_with_sd
   </td>
   <td>sandisk_sd_format_overwritten
   </td>
  </tr>
  <tr>
   <td>Ilość odzyskanych plików
   </td>
   <td>1729
<p>
1295
   </td>
   <td>1728
<p>
1294
   </td>
   <td>982
<p>
741
   </td>
   <td>1779
<p>
1360
   </td>
  </tr>
  <tr>
   <td>występujące kombinacje
   </td>
   <td>wszystkie
<p>
wszystkie
   </td>
   <td>wszystkie
<p>
wszystkie
   </td>
   <td>wszystkie
<p>
wszystkie
   </td>
   <td>wszystkie
<p>
wszystkie
   </td>
  </tr>
  <tr>
   <td>Ilość odzyskanych zdjęć w wysokiej rozdzielczości
   </td>
   <td>850
<p>
400
   </td>
   <td>849
<p>
399
   </td>
   <td>485
<p>
242
   </td>
   <td>883
<p>
447
   </td>
  </tr>
  <tr>
   <td>Ilość odzyskanych zdjęć wraz z dużą ilością metadanych
   </td>
   <td>1315
<p>
800
   </td>
   <td>1314
<p>
799
   </td>
   <td>736
<p>
483
   </td>
   <td>1324
<p>
839
   </td>
  </tr>
  <tr>
   <td>Ilość odzyskanych zdjęć bez metadanych
   </td>
   <td>410
<p>
494
   </td>
   <td>410
<p>
494
   </td>
   <td>245
<p>
257
   </td>
   <td>451
<p>
520
   </td>
  </tr>
  <tr>
   <td>Ilość odzyskanych plików video
<p>
(miniatur)
   </td>
   <td>70
<p>
85
   </td>
   <td>70
<p>
85
   </td>
   <td>10
<p>
12
   </td>
   <td>52
<p>
70
   </td>
  </tr>
</table>


Liczby nie kłamią - jak widać w powyższej tabeli, ilość odzyskanych plików i szczegółów dzięki **RescuePro **jest znacząco większa. Jest to najbardziej widocznie dla zdjęć w wysokiej rozdzielczości i metadanych - uzyskana liczba plików jest prawie dwukrotnie większa.

Najciekawsze dla mnie są wyniki odzyskanych filmów vs samych miniaturek - udało się odzyskać więcej miniatur niż samych plików video.

Program **RescuePro ** działał zdecydowanie szybciej oraz udało mu się odnaleźć znacząco więcej istotnych informacji - które zostały pominięte na przykładzie analizowanego oprogramowania Open Source. 

Myślę, że jeśli ktoś byłby bardzo zdesperowany do odzyskania dużej ilości plików a tym samym jak najdokładniej, to warto zapłacić za skorzystanie z **RescuePro**, jednakże dla osoby niewymagające zbyt wiele - foremost w zupełności wystarczy - przypadkowo usunięte cenne zdjęcia z wakacji nie są pod wielkim zagrożeniem 😁

W każdym razie można godnie polecić oba programy.


## Pakiet Adobe

Przeanalizowałem również pliki wyeksportowane za pomocą oprogramowania:



* Adobe Photoshop CC
* Adobe Premiere Pro CC
* Adobe Lightroom CC

I programy te zostawiają po sobie naprawdę sporo metadanych, większość oczywiście potrzebnych i _normalnych_, aczkolwiek jest też parę zastanawiających:

np. dokładną wersję oprogramowania,  dokładną historię działania programu, czas zapisania samych metadanych (ochrona integralności), XMP toolkit z datą, ścieżkę do pliku na systemie użytkownika!

zhashowane historia instancji i ID użytkownika i dokumentu (pliku) albo coś, co wygląda jak hash licencji 










Moim zdaniem, programy te zostawiają po sobie zbyt dużo metadanych, co może doprowadzić do negatywnych konsekwencji - np. do dopasowania konkretnej osoby (ID instancji) z danym plikiem lub w przypadku próby sfabrykowania podstawowych danych exif (np. daty utworzenia), jeśli osoba nie jest czujna, to nadal idzie wykryć tą prawdziwą datę - dostępną pod metadaną np. _History When_.

I właśnie tak się zdarzyło w historii - Donieckiej Republiki ludowej, kiedy filmik wyszedł niby dnia X i wtedy był aktualny, a tak naprawdę został zarejestrowany dwa dni prędzej (wychwycone za pomocą dokładnej analizy metadanych)


## Podsumowanie

Z każdej z powyższych opcji analizy, udało się coś ciekawego uzyskać (_sandisk_sd, sandisk_sd_format_, _lumix_with_sd, sd_format_overwritten)_

Formatowanie kart SD w normalny sposób nie jest bezpieczne. Za pomocą dostępnych narzędzi (zarówno darmowych jak i płatnych) można odzyskać naprawdę wiele plików i metadanych - a tym samym dowiedzieć się sporo o osobie, która z takowego nośnika korzystała. Dlatego sprzedawanie używanych kart SD lub np. pendrive'ów niesie za sobą co najmniej ryzyko skompromitowania prywatnych zdjęć użytkownika, ale również pozyskania cennych informacji o jego zachowaniach, zwyczajach, a nawet lokalizacji (dane geolokalizacyjne exif). 

Nie ma znaczenia w jaki sposób karta SD została sformatowana - czy to z poziomu komputera (GUI), czy za pomocą oprogramowania aparatu - **nie jest to bezpieczne**

Można również uzyskać informację z zepsutego nośnika (w tym przypadku skorumpowanego zrzutu pamięci), co może czasem okazać się przydatne.

Moim zdaniem, dostawcy sprzętu fotograficznego powinni w oprogramowaniach urządzeń umożliwiać dokładne formatowanie kart - wraz z wielokrotnym nadpisywaniem - tak, aby uniemożliwić odzyskanie wcześniej zapisanych danych. Oczywiście, takie rozwiązanie nie jest idealne, czasem zdarzy się sytuacja, gdy ktoś formatuje kartę przez przypadek i wtedy program do odzyskiwania danych okazuje się być zbawiennym; a sam proces dodatkowego nadpisywania danych pochłania zasoby oraz przede wszystkim czas. Jednakże, najlepiej aby użytkownik miał możliwość - czy chce sformatować kartę w sposób szybki (ale nie do końca bezpieczny) czy bezpieczny (aczkolwiek wolny).

Z tego co mi wiadomo i udało się znaleźć, to takie rozwiązanie zostało zaprezentowane w latach około 2010 jedynie w firmie **Leica. ** Żaden inny wielki producent nie poszedł tą drogą, a nawet sama Leica od tego odchodzi z uwagi na negatywne opinie użytkowników. Niestety, lata mijają a producenci aparatów nie idą w stronę bezpieczeństwa

Jednakże takowa opcja jest dostępna w systemie Windows - przy resetowania komputera do ustawień fabrycznych

 

Na koniec dnia, umiejętność wyważenia jednocześnie wygody i bezpieczeństwa (które niestety nie idą w parze) jest naprawdę pożądana.


# Przygotowanie środowiska


## 
    Narzędzia

Na potrzeby projektu  skorzystam z następujących narzędzi:



* Oracle Vm VirtualBox z zainstalowanym systemem Kali Linux/Linux Mint
* obraz systemu Windows użyczony od kolegi z kierunku

## 
    Zmiana ustawień systemowych

* zmiana motywu graficznego, tapety, window manager’a
* zmiana paru ustawień w rejestrze (xfwm4: wrap_cycle, box_move, zoom_desktop; xfce4-panel: position-locked,  enable-keyoboard-shortcuts)

* zmiana ustawień firewalla 

* instalacja zewnętrznego oprogramowania (steam, vlc, kleopatra, obs, teamviewer)
* pobrania plików graficznych
* 

* oraz utworzyłem parę plików w formatach docx, pdf, txt za pomocą różnych metod (terminal,  LibreOffice Writer, Google docs)
* utworzyłem przykładową sesję użytkownika internetu
* wrzuciłem zdjęcia wraz z lokalizacją GPS wykonane telefonem do systemu plików maszyny wirtualnej
* poprzenosiłem, pousuwałem parę plików, próbowałem ,,nadpisać” miejsca gdzie pliki zostały usunięte za pomocą plików o dużym rozmiarze

**3.**




Utworzenie pliku .raw


# **Autopsy**


### Utworzenie nowej sprawy w programie autopsy oraz wstępna konfiguracja

Dodaje domyślne moduły:







Warto zauważyć iż program zaraz po otwarciu rozpoczyna skanowanie w poszukiwaniu wszelkiego rodzaju plików




Będzie to dłuuugie skanowaanie…

Domyślnie obraz maszyny wirtualnej kaliego zajmuje 80 GB (dzieje się tak, ponieważ system plików był zaznaczony jako dynamiczny, a przecież maszyna i tak musi zgoła znać jakąś dolną granicę - analogia do pamięci VSZ i RSS), dlatego do dalszej analizy w programie autopsy użyję obrazu systemu Windows utworzonego przez kolegę z kierunku. 

Na wstępie pragnę zaznaczyć, iż postaram się zdobyć jak najwięcej informacji o samym użytkowniku i jego aktywnościach, a następnie porównam owe informacje z tym, co faktycznie zrobił kolega, który użyczył mi obrazu systemu.

 

Już przy pierwszych chwilach jesteśmy witani errorem 




Prawdopodobnie świadczy to o tym, iż plik który powinien był się tam znajdować, został nadpisany, dlatego trzeba mieć na uwadze, że nie wszystkie pliki pójdzie odzyskać


### **Z jakiego systemu korzystał nasz użytkownik?**

Po samej strukturze plików łatwo jest stwierdzić, że jest to windows, tylko należy zadać pytanie jaki dokładnie?

Udało mi się odnaleźć plik odpowiadający na to pytanie




 oraz architektura procesora i nazwa komputera


### **Informacje o samych użytkownikach?**

Dlaczego liczba mnoga?

Ponieważ w

C:\windows\system32\config\SAM\Domains\Account\Users\Names możemy znaleźć iż faktycznie było ich więcej niż sam użytkownik o nazwie Cyberniebezpieczny

Widnieje też _Sąsiad Zbigniew_ oraz _Hektor_

Potwierdza to również inna zakładka** OS Accounts **

Gdzie mamy również mamy informacje o tym, ile razy dany użytkownik się zalogował, oraz czy używał hasła


### **Urządzenia**

** Data Artifacts > USB Device Attached:**

W zakładce tej możemy wyczytać iż użytkownik korzystał z jakiegoś urządzenia o nazwie_ ROOT_HUB30_, po lekkim researchu można stwierdzić iż jest to po prostu sterownik intela




Inną ciekawą rzeczą w tej zakładce jest urządzenie o nazwie _USB Tablet_, którego _twórcą_ był VirtualBox - i na tej podstawie możemy określić iż dołączone urządzenie nie było fizyczne - tylko wirtualne/logiczne. Daje nam to podstawy do przypuszczania, że nasz główny użytkownik korzystał właśnie z oprogramowania do wirtualizacji


### **Analiza metadanych EXIF**




W podglądzie zakładki widnieje liczba plików 34, w rzeczywistości liczba <span style="text-decoration:underline;">unikalnych </span>zdjęć to 17. 




Dzieje się tak, ponieważ użytkownik przechowywał te pliki w folderze User/Pictures, a następnie usunął je do kosza (a mimo wszystko udało się je stamtąd odzyskać)

Przejdźmy teraz do analizy lokalizacji




Udało się odnaleźć 2 znacząco odległe od siebie miejsca.



1. zdjęcie _Wieza w pizie.jpg_ gdzie faktycznie jest ukazana wieża Eiffla. Co może zaskakiwać, jest fakt, że znaczniki lokalizacji zostały zmanipulowane (najprawdopodobniej programem typu ExifTools) - wskazują one nie na paryż, a środek pustyni w Nigrze. 






2. Drugim obrazem jest jakaś świątynia/monument. Po odrobinie analizy OSINT jesteśmy w stanie stwierdzić, że jest to Panteon znajdujący się w Paryżu. Warto zauważyć, że znaczniki GPS faktycznie wskazują na to miejsce




Większość zdjęć zrobionych smartfonem Nexus znajduje się w tej samej lokalizacji - Francji, Paryżu

Można przypuścić iż użytkownik był tam na dłużej na jakimś wyjeździe/wakacjach, gdzie zajmował się zwiedzaniem miasta. Przedział datowy to 2012.07.24-26 a więc 3 dni

Co ciekawe, program autopsy nie znalazł nam lokalizacji zdjęcia o metadanych znajdującego się w Anglii

Okazuje się, że jest to tylko tapeta (prawdopodobnie autor fotografii zapomniał usunąć wrażliwych metadanych). Może o tym fakt iż fotografia była w folderze _pobrane _oraz miała inną datę 

Korzystając z darmowej aplikacji javowej **GeoTag **oraz analizująć czas wykonania zdjęcia oraz same lokalizacje, jesteśmy w stanie utworzyć trasę, po której poruszała się ta osoba




Jak widać, są to jedne z najbardziej charakterystycznych miejsc paryża

W ramach ciekawostki mogę dodać iż program ten pozwala nam na głębszą analizę typu OSINT - przykładowo zaznaczając, w którą stronę zostało wykonane zdjęcie na podstawie informacji charakterystycznych elementów otoczenia (ikona i), czy cieni

Ciekawostka #2 - **Geoguessr **istnieje aplikacja/gra w której losujemy dowolne miejsce na świecie - i na podstawie przemieszczania się po okolicy za pomocą widoku google maps - musimy odgadnąć dokładną lokalizację. Przydatne mogą się okazać wtedy np. charakterystyczne budynki, język na bilbordach, zabudowa, obecność wody itd.

Może być przydatna w ćwiczeniu umiejętności analizy OSINT’owej oraz zakresu informatyki śledczej

Niestety, cała powyższa aktywność użytkownika Cyberniebezpiecznika (związana z robieniem zdjęć) nie została stworzona przez niego - a pobrana z internetu

Świadczyć może o tym fakt, że w zakładce Web Downloads są ślady właśnie tych zdjęć







Kolejne potwierdzenie - w usuniętych plikach z folderu Downloads było archiwum .zip o świadczące o paczce obrazków wraz z metadanymi gps





### **Historia wyszukiwarki**

**Co możemy dalej powiedzieć o użytkowniku?**  Pokaż mi historię wyszukiwarki a powiem ci kim jesteś




Na tej podstawie można najprawdopodobniej stwierdzić że użytkownik



1. korzystał z wyszukiwarki bing i przeglądarki Microsoft Edge
2. bardzo interesuje się gotowaniem makaronu
3. sprawdzał, czy jego hasło jest bezpieczne
4. szukał jak wypasa się owce w bieszczadach
5. pracuje albo chce pracować we frontendzie


### **Web History **

Znaleziony plik WebCacheV01.dat posiada ślady próby logowania do usług microsoft’u, z zapytania w przeglądarce jesteśmy w stanie wydobyć id użytkownika




Również w WebHistory udało mi się znaleźć ślady o pliku _notAScript.jpg.py_ (ślady znalezione dzięki plikom z rozszerzeniem .lnk), ciężko było go pierwotnie znaleźć w systemie plików - aczkolwiek udało się. Plik udomowił się w katalogu /System32/bg-BG/DoNotDelete/ 




Jest to niegroźny malware? 

Najprawdopodobniej nasz nieświadomy użytkownik pobrał taki plik z internetu

Z tego co udało mi się wyczytać, pliki z rozszerzeniem .lnk są automatycznie tworzone przez system windows w trakcie otwarcia pliku/programu. Może się to okazać kluczowa funkcjonalność dla późniejszej analizy obrazu systemu (lub analizy szkodliwego oprogramowania)


### **Inne ciekawe pliki**

Zakładka FIle Views > FIle Types > By MIME Type >

znajdziemy plik, który pierwotnie znajdował się w katalogu Cyberniebezpieczny/Music




Zdecydowanie jest to ślad związany z życiem prywatnym użytkownika

Parę wydobytych zrzutów ekranu:




Usuniętych programów








 oraz plików tekstowych i archiwum .gz


# **Zrzut pamięci RAM oraz jej analiza**


### **Narzędzia**

Na potrzeby powyższego zadania skorzystam z maszyny wirtualnej (na VirtualBox’ie)  + programu **Volatility** do analizy 


### **Przygotowanie maszyny wirtualnej**

Najpierw stworzyłem zwykłą sesję użytkownika - otwartych parę kart przeglądarki, uruchomiony odtwarzacz zdjęć i filmów, eksplorator plików, procesy w terminalach, uruchomione pobieranie w tle

W sumie wykorzystałem około połowę dostępnej pamięci RAM

Następnie odłączyłem maszynę od sieci i odpaliłem [malware ](https://github.com/Anish-M-code/Cstorm-windows-startup-virus-in-c/tree/master/Binaries)- backdoora + keyloggera




Najpierw tworzę zrzut pamięci RAM uruchomionej maszyny wirtualnej za pomocą debuggera programu Virtualbox - musiałem to zrobić naprawdę szybko, ponieważ od razu po odpaleniu malwer’a system postanowił się wyłączyć, dlatego jednym z celów tego zadanie będzie:

Jak wiele można odzyskać/wyczytać z analizy pamięci RAM z wyłączającego się systemu?


### **Volatility**


#### windows.info

Podstawowe informacje komendy parametru **windows.info** (używam symboli windowsowych):







Możemy się stąd dowiedzieć trochę o samym systemie i sprzęcie - przykładowo, że jest 64 bitowy, używa procesora intela, jaką ma aktualną godzinę, wersję systemu operacyjnego (Windows 10)


#### pslist

Przejdźmy teraz do nieco ciekawszej komendy - listy wszystkich procesów **pslist**




Przykładowy zrzut ekranu (nie uda się zamieścić całości wyjścia tej komendy, ponieważ jest zbyt długie). Można znaleźć tutaj takie informacje jak: numer procesu i jego rodzica, nazwa procesu, miejsce w pamięci (zapisane szesnastkowo), liczbę wątków, data utworzenia

Sprawdźmy ile różnych procesów było uruchomionych:




**42** - z czego znacząca większość to procesy systemowe




Są to między innymi: odtwarzacz muzyki, przeglądarka explorer i edge, eksplorator plików, menadżer zadań, sklep microsoft oraz wiele więcej _tajemniczych_ procesów

Dlaczego są tam procesy virtualboxa?


Proces [VBoxTray](https://www.file.net/process/vboxtray.exe.html) oraz [VBoxService](https://www.file.net/process/vboxservice.exe.html) tak naprawdę nie ma nic wspólnego z programem VirtualBox, jest to program do zarządzania innymi programami, obsługą klawiatury oraz myszki


#### pstree

Sprawdźmy drzewo procesów **pstree**:


Sortując procesy po czasie można dokładnie określić co i w jakiej kolejności użytkownik robił.

Jesteśmy w stanie prześledzić proces instalacji malware

**TiWorker** - proces odpowiedzialny za instalacje programu >  **TrustedInstall** - nadanie uprawnień instalacji

Dwa powyższe procesy są to dzieci procesu systemowego - **svchost.exe **oraz** services.exe**. Można to łatwo zobaczyć dzięki stukturze drzewa komendy **pstree**




**wlrmdr.exe** najprawdopodobniej jest to właśnie interesujący nasz malware, ponieważ od razu po nim nastąpiło wylogowanie się systemu windows (przypadłość, którą wcześniej opisałem) oraz jest to pojedynczy proces. Poza tym, udało mi się znaleźć [informację](https://www.file.net/process/wlrmdr.exe.html) iż czasem pod taką nazwą pliku mogą kryć się groźne programy. Jest to wstępne założenie, które być może później uda się potwierdzić/obalić

Również dzięki takiemu ułożeniu graficznemu można zobaczyć jak uruchamiają się karty przeglądarki





#### malfind

Przejdźmy do wbudowanego narzędzia do analizy malware - **malfind**







Wyszukuje on procesów które mają wszystkie 3 uprawnienia - read, write, execute - ponieważ działający malware z reguły takowe posiada




O dziwo, jedyne co udało się znaleźć to procesy o nazwie - _MsMpEng_ - rozwinięcie tego skrótu to Microsoft Malware Protection Engine, a więc jest to Windows Defender

W takim razie, w tej kategorii nie udało się odnaleźć potencjalnego procesu z malwarem


#### filescan

Sprawdźmy teraz ogólne pliki, które udało się znaleźć




Jest ich naprawdę sporo - około 9 tysięcy







Można tutaj znaleźć przykładowo sterowniki oraz pliki systemowe oraz generalną strukturę plików użytkownika 

Znając nazwę wirusa, sprawdzam, co udało się programowi Volatility odzyskać




Możemy tutaj odnaleźć parę plików wykonywalnych z rozszerzeniem .exe

Udało się odnaleźć wcześniej wspomniany plik, podejrzany o bycie malwarem




Jest on udomowiony w katalogu \System32, co znacząco zmniejsza szanse na bycie plikiem złośliwym - a tylko procesem systemowym

[https://fileinfo.com/extension/mui](https://fileinfo.com/extension/mui)




W tym plik instalacyjny złośliwego oprogramowania + jego sygnatura


#### Sygnatura pliku

Przejdźmy zatem do tejże sygnatury pliku




Niestety nie udało się odzyskać pliku


#### hivelist




Komenda ta pozwala na wydobycie logów z rejestru systemu.  Można tu znaleźć wiele interesujących ustawień i konfiguracji.

Jest tu między innymi plik odpowiedzialny za ustawienia użytkownika - a w nim hash hasła

Potrzebny będzie adres komórki pamięci, gdzie znajduje się interesujący plik - w tym przypadku 0x8783bca25000

Niestety komendzie nie udało się nic znaleźć. Może być to związane z tym, że nie istnieją wskaźniki tego pliku, lub są uszkodzone




Tak samo nie działa komenda **dumpfiles**


#### 


Myślę, że niedziałający charakter poszczególnych komend ma związek z tym, że zrzut pamięci RAM stworzyłem w trakcie wyłączania się systemu 


#### netscan 

sprawdźmy, czy złośliwy proces próbował się połączyć z siecią (według działania jego kodu powinien) za pomocą komendy **windows.netscan**




Warto zauważyć, że powyższa komenda wykonywała się naprawdę długo 




Przykładowy output

Są tutaj dostępne parametry takie jak: miejsce w pamięci, protokół sieciowy, adres źródła i docelowy, port, data wykonania oraz informacje o procesie.

Oczywiście, jest dużo informacji o samych procesach systemowych Windows’a - svchost, cortana, wininit

Na podstawie powyższego output’u można stwierdzić z jakimi serwerami (stronami) próbował połączyć się użytkownik za pomocą przeglądarki




Przeanalizowałem parę dostępnych adresów i udało mi się ustalić, że użytkownik wysyłał zapytania na adres ip serwerów GitHuba (co jest prawdą)




Na podstawie samych adresów można stwierdzić, skąd pochodzi użytkownik (o ile nie używa serwera proxy/vpn’a)




W tym przypadku zapytania przechodziły przez polskie serwery

Niestety nie udało się znaleźć próby połączenia tego procesu z siecią (problemem może być to że nie zdążył się on wykonać - albo nie nastąpiło ustanowienie sesji z powodu odłączenia maszyny wirtualnej od internetu) 


### Podsumowanie

Łącząc wszystkie powyższe fakty, dochodzę do wniosku, że faktycznie złośliwa część kodu programu, który został podejrzewany o bycie malware’m, nie została wykonana poza samym zainstalowaniem, jakakolwiek część działania programu nie została zainicjalizowana


# Wykorzystaniu narzędzia ExifTool do wyciągnięcia metadanych z plików graficznych oraz przedstawienia metod odzyskiwania straconych danych.


## analiza odzyskiwania plików obrazu systemu maszyny wirtualnej


### 
    foremost

rozpoczynam skanowanie za pomocą komendy _foremost windows_




Powyższy program układa output w sposób zorganizowany, dzieląc na podfoldery w zależności od rozszerzenia




Udało się znaleźć trochę plików w różnych rozszerzeniach

W poniższej analizie zajmę się głównie plikami graficznymi - jpg, mov, mp4, png.

Na starcie warto zaznaczyć iż plików jest tak dużo, ponieważ zeskanowany został cały obraz systemu Windows - a w nim jest dużo plików graficznych potrzebnych, aby oprogramowanie działało sprawnie (ikony, animacje itp.)


#### JPG

Łącznie Udało się znaleźć 457 plików. Jest to całkiem spora ilość, dzięki której jest możliwość analizy indywidualnej sesji użytkownika

Przykładowe odnalezione zdjęcia:







Na pierwszy rzut oka zdjęcia pochodzą z:



1. systemu windows
2. artykułów
3. indywidualnych preferencji użytkownika
4. galerii tapet


Co dziwne, udało się znaleźć parę plików o rozmiarze 1 x 1 pixel. Trochę nie widzę sensu, dlaczego system operacyjny miałby przetrzymywać takie pliki. Prawdopodobnie jest to obrazek pobrany automatycznie w wyniku śledzenia strony internetowej/maila - tak zwany_ pixel tracking_ lub _web beacon_

Poniższy obraz o rozmiarze 1 x 800 pixeli, również jest trochę dziwny, aczkolwiek jestem w stanie wyobrazić sobie jego zastosowanie w systemie


#### 





Jest też parę uszkodzonych plików

Posortowałem odnalezione pliki według rozmiaru - ponieważ zdjęcia zrobione przez użytkownika powinny mieć większy rozmiar niż przykładowo ikony systemowe




Niestety, owych zdjęć jest naprawdę mało, a w żadnym z nich nie zostały zachowane dane exif

Jedynie udało się odzyskać trochę danych z podstawowej tapety systemu Windows





#### PNG

Przykładowe pliki:


Tutaj będziemy mieli do czynienia ze znacznie większą ilością plików systemowych




Podobnie jak w przypadku plików z rozszerzeniem .JPG - nie udało się odnaleźć praktycznie żadnych danych exif, nawet w plikach wyglądających na zdjęcia użytkownika


#### MOV


# 


Udało się odnaleźć aż 52 elementy, jednakże kompletnie wszystkie są uszkodzone - brak miniaturek, video się nie odtwarza

Natomiast zachowało się trochę metadanych - m.in. dostawca, data utworzenia, software




Choć wnioskując po tytule - _Skype_Shutter_Master_v2_11JUN15_mono_ - są to video pochodzące z domyślne dostępnego w systemie Windows programu do rozmów **Skype**


#### MP4

Odnalezione pliki nie posiadają wcale obrazu - jest tylko czarny ekran.

Z drugiej strony dźwięk w nich został zachowany. Jednakże większość dźwięków tych plików brzmi jak nie z tej ziemi, dodatkowo są przesterowane




Jednakże udało mi się odnaleźć 2 filmiki, w których występuje dzwonek programu skype (choć też z przesterami), jest to o tyle dziwne, ponieważ jest to w rozszerzeniu .mp4, czyli typowo filmowym a nie dźwiękowym




Sprawdziłem wartość hash powyższego pliku




I faktycznie jest to plik należący do Microsoftu - służący jako dzwonek

Większość metadanych plików została zachowana




Można znaleźć takie informacje jak np. - data utworzenia, dane dostawcy czy bitrate


### Recoverjpeg

Rozpoczynam skanowanie za pomocą komendy




Udało się odnaleźć 307 plików, jest to znacząco mniejsza liczba niż w przypadku programu **foremost**

Przykładowy wynik




Niestety również metadane nie zostały zachowane, narzędzie to nie pomogło


### Bulk_extractor

Przyszła teraz pora na program, który jest znany z wydobywania metadanych - **bulk_extractor.** Zobaczymy, czy pomoże rozwiązać problem ich braku w analizowanym obrazie

Uruchamiam program komendą




Generalnie efektywność bulk_extractora można opisać jako dość biedną - jednakże jego siła kryje się w wszelkich innych typach plików i sygnaturach 

Potrafi wydobyć przykładowo linki url zawarte w systemie, numery telefonów, adresy email, zapytania DNS




Program ten zbiera odnalezione dane i dzieli je na pliki tekstowe - zawierające szczegółowe informacje - oraz foldery z odzyskanymi plikami


#### GPS

Programowi udało się wydobyć trochę danych lokalizacyjnych - czego nie udało się reszcie (mimo przeszukania wszystkich metadanych plików za pomocą komend powłoki bash)




Sprawdźmy więc przykładowe dane:







Lokalizacja wskazuje typowo na paryż, co jest zgodne z analiza przeprowadzoną z użyciem programu autopsy

Dostępne są również daty utworzenia danego zdjęcia - wskazują na rok 2012, co również się zgadza

Co więcej, udało się odzyskać metadane z wszystkich zdjęć podróży użytkownika - jednakże z małym problemem, któr opiszę poniżej


#### JPG




Niestety, w folderze _jpg_carved_ udało się znaleźć zaledwie 2 pliki, co jest naprawdę małą ilością zważając na inne programy (np. ponad 400)


### Podsumowanie

Generalnie każde z powyższych narzędzi w jaki sposób spełniło swoją rolę - z tym, że istnieją narzędzia, które łączą plusy każdego programu a nawet są bardziej efektywne (mowa tutaj o np. **autopsy, RescuePro**). W przypadku analizy obrazu systemu można znaleźć wiele plików należących do systemu operacyjnego, co znacznie utrudnia pracę i ciężko jest przypisać konkretne dane do użytkownika


# Wyciągnięciu danych ze smartfona. Analiza plików (PLIST i SQLite). 

Na potrzeby następującej analizy skorzystam z gotowego obrazu pobranego z MS Teams oraz narzędzia polecanego przez **NIST**: modułu **Autopsy Android/iOS **

Również przeanalizuję bazę danych - plik accounts3.sqlite


### Analiza sqlite

Sprawdź dostępne _tables_:




Za pomocą komendy SELECT ZUSERNAME, ZACCOUNTDESCRIPTION FROM ZACCOUNT:


Można wydobyć adres email użytkownika - **[thisisdfir@gmail.com](mailto:thisisdfir@gmail.com)**

Na sam start warto zaznaczyć iż użytkownik korzystał z telefon  systemem iOS










Następnie sprawdzam każdą tabelę

Jednakże nie udaje mi się odnaleźć nic ciekawego, a sama metodologia jest czasochłonna i mało efektywna

Dlatego przechodzę do następnego narzędzia - Autopsy z modułem iOS analyzer:


### Przygotowanie środowiska




Dodaję nową sprawę do programu

Następnie upewniam się, aby moduły iOS i Android Analyzer były zaznaczone




Dodaję folder do programu Autopsy

Rozpoczyna się skanowanie plików





### Analiza Autopsy

Po skończonym skanowaniu, struktura programu wygląda w następujący sposób:





#### TimeLine




Analizując powyższy wykres można stwierdzić, iż aktywność użytkownika przypada na okres lat 2020-2022

Czerwone - system plików - metadane z nich niekoniecznie mogły zostać stworzone w czasie kiedy użytkownik korzystał z systemu

Zielone - Web Activity, głównie korzystanie z przeglądarki i mediów społecznościowych (reddit, instagram, mewe, tiktok)

Niebieskie - Inne

Szczególnie intensywny okres przypada na marzec/kwiecień 2020


#### Call Logs




Użytkownik wykonywał połączenia z następującymi numerami:



* +14082560700
* +19193643164
* +17042751134
* 9197627808
* 9192853680

Wszystkie te połączenia były wykonane na przełomie 9-12 kwietnia 2020


#### Lokalizacja GPS

Przechodzę do zakładki _Geolocation_

Cała aktywność okresu korzystania użytkownika  z telefonu mieści się w jednym miasteczku w Stanach Zjednoczonych -> Holly Springs

Znalezione dane można pogrupować na 3 kategorie:



1. Zdjęcia zrobione telefonem wraz z zostawioną, włączoną lokalizacją GPS

    



    Dostępne są tylko 4 takie pliki, co nie jest zbyt dużą liczbą jak na telefon - niektórzy użytkownicy trzymają ich tysiące w pamięci


    Na powyższych zdjęciach można zobaczyć osiedle znajdujące się w tym miejscu na mapie - i faktycznie tam jest - porównując ze Street View map Google

2. Współrzędne lokalizacyjne sieci wi-fi

    



    A jest ich naprawdę dużo, obejmują zasięg połowy miasteczka.


    Interesujący jest fakt, że telefon zapisuje te dane w takiej ilości i robi to często (można wywnioskować po odstępach _Timestamp’u_ kolejnych plików)


    Na podstawie gęstości występowania znaczników, można stwierdzić w jakich miejscach użytkownik przebywał najczęściej - a tym samym określić miejsce jego zamieszkania - prawdopodobnie okolice _228 Whisk Fern Way, Holly Springs, NC 27540, Stany Zjednoczone_


    


3. Lokalizacja zaparkowania samochodu




Jest to ślad działania usługi [map Apple](https://support.apple.com/pl-pl/guide/iphone/ipha13ef1c2e/ios), która zostawia znacznik lokalizacyjny samochodu, kiedy telefon rozłączy się z usługi Bluetooth z Apple CarPlay




Co ciekawe, oprócz zaparkowanego samochodu można również znaleźć informację o lokalizacji auta na żywo/w ruchu

Na podstawie tych danych można stwierdzić zarówno miejsce zamieszkania użytkownika jak i siedzibę firmy, w której pracuje ta osoba


#### Urządzenia BlueTooth

W zakładce _BlueTooth Pairings_ dostępnych jest ponad 50 rekordów i jedyne widoczne urządzenia to słuchawki AirPods, które mają 2 różne nazwy:



* Josh’s AirPods
* This Is’s AirPods

Na tej podstawie można stwierdzić iż imię użytkownika telefonu to najprawdopodobniej **Josh**




Chociaż w zakładce USB Device Attached można znaleźć informację, że użytkownik korzystał z Apple Watcha - 40mm GPS + Cellular Apple Watch Series 4 (Watch4,3 model)


#### Calendar Entries




Można stąd wydobyć historię zdarzeń, na które najprawdopodobniej poszedł nasz użytkownik 

Większość z dostępnych wpisów to niestety bardzo ogólne, święta narodowe/dni wolne

Jedynym _prywatnym _wpisem, który udało mi się znaleźć, jest _Pick up lunch _ - jako jedyny ma ustawioną _normalną_ godzinę (15:00), zamiast 23:59:59


#### Oprogramowanie

Według programu, użytkownik ma zainstalowane 434 programy - jest to naprawdę duża liczba. Myślę że liczba jest tak wielka z powodu również wliczania się aplikacji systemowych (np. HomeUIService.app) lub niektóre są powielone

Przykładowa lista programów użytkownika

Znajdziemy tutaj:



* różne komunikatory np. messenger, signal, whatsapp, discord
* media społecznościowe np. tiktok, facebook, instagram
* aplikacje systemowe typu mapy, eksplorator plików, przeglądarka
* skrzynkę pocztową ProtonMail


#### Messages

Chyba jest to najciekawsza zakładka, ponieważ SMS’y nie są szyfrowane i można zobaczyć ich zawartość plaintextem




Można tutaj zobaczyć konwersacje użytkownika z inną osobą - pisząc o słynnym _green bubble_ w przypadku urządzeń z iOS

Warto zaznaczyć, że w tej zakładce nie da się wydobyć załączników - które użytkownik najprawdopodobniej przesłał

Można również odnaleźć kody weryfikacyjne do usług wymagających 2FA (Google, Tiktok, Signal, Viber, Telegram). 

Jest to idealny przykład, dlaczego uwierzytelnianie dwuskładnikowe w postaci kodów SMS nie jest do końca bezpieczne - wiadomości są relatywnie łatwe do przechwycenia (atak typu _man in the middle_ - np.używane przez NSA tzw _jaskółki_) a fakt, że nie są szyfrowane pozwala na ich łatwe odczytanie


#### Events

Jest to zakładka do przeznaczona do wyświetlania powiadomień systemowych:



* o poziomie baterii
* podłączeniu urządzenia do ładowania
* włączeniu trybu _do not disturb_
* zablokowaniu urządzenia
* zmianie orientacji ekranu
* używaniu Siri

Jednakże najdziwniejsze wydaje się powiadomienie o obudzeniu się użytkownika wraz z dokładną datą! Fakt ten może być dyskusyjny jeśli chodzi o prywatność





#### Web Search i Web History




Można tutaj zobaczyć strony, z którymi łączył się użytkownik wraz z dokładną datą.

Na tej podstawie można stwierdzić preferencję użytkownika

Użytkownik wyszukiwał:



* strony apple.com wraz z urządzeniem Ipad Pro
* kiedy zaczyna się _mlb 2020_ oraz informacje o sędzim
* czy NHL będzie kontynuowane
* serwis dfir.pupub.org


### Podsumowanie

Z urządzenia mobilnego można znacznie więcej zyskać niż z tradycyjnego komputera. Geolokalizacja urządzenia pozwala na odtworzenie dokładnej ścieżki po której poruszał się użytkownik, określenia miejsca zamieszkania/pracy, odtworzeniu gdzie znajdują się sieci wi-fi. Poza tym można łatwo przechwycić wrażliwe wiadomości w postaci SMS’ów. Również łatwo jest zobaczyć ważne informację o zdrowiu fizycznym użytkownika - np. kiedy się budził.

Generalnie rzecz biorąc, telefon zbiera znaczne ilości danych i jeśli takowe dane trafią w ręce osób niepowołanych, to mogą wywołać niemałe szkody - a co dopiero nie mówiąc o naruszeniu prywatności.


# **Utworzenie kopii binarnej nośnika **


### ewfacquire

Na potrzeby późniejszej analizy konwertuje plik .raw na format czytany przez narzędzia ewf - ewfacquire







Ustawianie podstawowych parametrów

Warto zaznaczyć iż blok ma **64 sektory**, a każdy sektor ma **512 bajty**




Jak widać konwersja przeszła pomyślnie


### hash

hash (może przydać się później przy sprawdzaniu integralności danych): d40647c0fd182404e81d4b91eb3a074b


### zamontowanie obrazu

Przechodzę do zamontowania obrazu w folderze /mnt/ tak, aby miał on formę fizycznego (lub logicznego) obrazu dysku





### System plików

Następnie trzeba ustalić system plików, który jest używany w naszym obrazie dysku. W przypadku systemów unixo-pochodnych najczęściej jest to .ext4




I rzeczywiście, interesujący nas system plików to ext4




Mając powyższe informacje (offset sector, block size) możemy przejść do analizy grup bloków


### Analiza grup bloków




Mamy dostępnych** 641 grup bloków** - co wydaje się naprawdę sporą liczbą - a samych bloków na grupę: **32768**

Liczba I-węzłów na grupę: **8192**


### Analiza plików

Przejdźmy więc do analizy znajdujących się w środku plików za pomocą komendy **fls**




Idąc ścieżką /home/Downloads możemy trafić na parę interesujących plików





### Odzyskanie pliku

Spróbujmy teraz odzyskać przykładowy plik: SarmataFlag.png

Wiemy iż ma on następujący numer inode: 2100382

Za pomocą komendy icat jesteśmy w stanie uzyskać w pełni powyższy plik:




Jednak jest to format binarny, nieczytelny dla człowieka. W takim razie skierujmy output do pliku .png







Plik został odzyskany 😎


### Exif

Można następnie przejść następnie do analizy danych exif (już lokalnie)




Niestety nie ma tu nic ciekawego

Znajdźmy plik, który jest zdjęciem ze smartfona (najlepiej z danymi lokalizacji GPS)





Udało się pomyślnie odzyskać zdjęcie w formacie .jpg


### fls

Jeśli chodzi o dalsze przeszukiwanie systemu plików za pomocą komendy **fls** - ciekawy może okazać się folder /media. Znajdują się tam podfoldery używane do zamontowania obrazów np. pendrive'ów, cd rom'ów, a nawet dysków chmurowych




Tutaj zostało znalezione medium “sf_is”

Niestety, wchodząc w jego numer i_node nie dostajemy żadnego outputu




Może być to spowodowane niepoprawnym skonfigurowaniem ów dysku przez użytkownika


### Odzyskiwanie hasła

Spróbujmy teraz odzyskać hasło, którego używał użytkownik systemu

Hash hasła znajdziemy w /etc/shadow







oraz plik /etc/passwd 







Trzeba jeszcze sprawdzić jakiego algorytmu jaki był algorytm haszujący użytkownika

Rozpoczynam działanie programu **hashcat**




I za pomocą metody brute force + słownika udało mi się odzyskać jakże nieskomplikowane hasło - **_kali_**
