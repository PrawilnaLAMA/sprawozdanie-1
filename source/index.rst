.. Sprawozdanie documentation master file, created by
   sphinx-quickstart on Thu Apr  4 13:08:26 2024.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

========================================
Bezpieczeństwo baz danych
========================================

.. toctree::
   :maxdepth: 2
   :caption: Spis treści:

    """
=============================================================
Zarządzanie Bezpieczeństwem w Systemach Baz Danych PostgreSQL
=============================================================


pg_hba.conf - Sterowanie Dostępem
=================================

Plik ``pg_hba.conf`` (PostgreSQL Host-Based Authentication) jest
kluczowym elementem zarządzania bezpieczeństwem w PostgreSQL. Umożliwia
on kontrolę dostępu do serwera bazy danych na podstawie adresów IP,
typów połączeń oraz mechanizmów uwierzytelniania.

Struktura pliku pg_hba.conf
---------------------------

Plik ``pg_hba.conf`` składa się z linii, z których każda definiuje
regułę dostępu. Każda linia zawiera następujące pola:

-  **typ połączenia** - określa, czy połączenie jest lokalne
   (``local``), czy zdalne (``host``).

-  **baza danych** - nazwa bazy danych, do której dostęp jest
   kontrolowany.

-  **użytkownik** - nazwa użytkownika bazy danych.

-  **adres** - adres IP klienta (dla połączeń zdalnych).

-  **metoda uwierzytelniania** - określa mechanizm uwierzytelniania,
   który ma być używany.

Przykład reguły w pliku ``pg_hba.conf``:

::

   # Typ    Baza danych    Użytkownik    Adres              Metoda
   host     all            all           192.168.1.0/24     md5

Mechanizmy Dostępu
------------------

Plik ``pg_hba.conf`` definiuje kilka mechanizmów uwierzytelniania,
takich jak:

-  **trust** - pozwala na dostęp bez uwierzytelniania. Jest to opcja
   najmniej bezpieczna i powinna być używana tylko w wyjątkowych
   przypadkach.

-  **md5** - wykorzystuje hasła zaszyfrowane algorytmem MD5. Jest to
   standardowy mechanizm uwierzytelniania.

-  **password** - wymaga podania hasła w postaci nieszyfrowanej. Jest to
   mniej bezpieczne niż ``md5``.

-  **peer** - umożliwia uwierzytelnianie na podstawie identyfikatora
   systemowego użytkownika.

-  **cert** - wykorzystuje certyfikaty SSL do uwierzytelniania.

-  **scram-sha-256** - nowoczesny i bezpieczny mechanizm
   uwierzytelniania, który wykorzystuje algorytm SCRAM-SHA-256.

Konsekwencje Wyboru Mechanizmu Dostępu
--------------------------------------

Wybór odpowiedniego mechanizmu uwierzytelniania ma bezpośredni wpływ na
bezpieczeństwo systemu. Mechanizmy takie jak ``trust`` mogą znacznie
obniżyć poziom bezpieczeństwa, podczas gdy ``cert`` w połączeniu z SSL
zapewnia wysoki poziom ochrony danych. Warto dokładnie analizować
potrzeby i ryzyka związane z każdym mechanizmem uwierzytelniania.

Na przykład, stosowanie mechanizmu ``trust`` może być akceptowalne w
przypadku baz danych używanych wyłącznie w środowisku testowym, gdzie
bezpieczeństwo nie jest priorytetem. Natomiast w środowisku
produkcyjnym, gdzie przetwarzane są dane wrażliwe, konieczne jest użycie
bardziej zaawansowanych mechanizmów, takich jak ``md5``,
``scram-sha-256`` lub ``cert``.

Przykłady Konfiguracji
----------------------

Przykładowe konfiguracje pliku ``pg_hba.conf`` mogą obejmować różne
scenariusze dostępu:

::

   # Uwierzytelnianie lokalne dla superużytkownika
   local   all             postgres                                peer

   # Uwierzytelnianie lokalne dla wszystkich użytkowników za pomocą hasła MD5
   local   all             all                                     md5

   # Uwierzytelnianie zdalne dla sieci lokalnej z użyciem hasła MD5
   host    all             all             192.168.1.0/24          md5

   # Uwierzytelnianie zdalne z użyciem certyfikatów SSL dla wszystkich baz danych
   hostssl all             all             0.0.0.0/0               cert

Podsumowanie
------------

Plik ``pg_hba.conf`` jest nieodzownym elementem zarządzania dostępem do
baz danych PostgreSQL. Dzięki możliwości definiowania szczegółowych
reguł dostępu, administratorzy mogą precyzyjnie kontrolować, kto i w
jaki sposób może łączyć się z bazą danych, co jest kluczowe dla
zapewnienia bezpieczeństwa systemu.

Uprawnienia Użytkownika
=======================

Uprawnienia użytkownika w PostgreSQL są zarządzane na kilku poziomach:
systemu zarządzania bazą danych (DBMS), poszczególnych baz danych oraz
tabel.

Poziom DBMS
-----------

Na poziomie DBMS uprawnienia mogą obejmować możliwość tworzenia nowych
baz danych, zarządzanie użytkownikami oraz konfigurację systemu.
Przykładowe polecenia to:

::

   GRANT CREATE ON DATABASE dbname TO username;
   REVOKE CREATE ON DATABASE dbname FROM username;

Administratorzy bazy danych (DBA) mają pełne uprawnienia na poziomie
DBMS, co pozwala im na zarządzanie wszystkimi aspektami działania
systemu PostgreSQL. Uprawnienia te mogą obejmować:

-  Tworzenie i usuwanie baz danych.

-  Tworzenie i zarządzanie użytkownikami oraz rolami.

-  Konfigurację parametrów systemowych i optymalizację działania bazy
   danych.

Poziom Bazy Danych
------------------

Na poziomie bazy danych uprawnienia mogą obejmować dostęp do danych,
modyfikację struktur oraz wykonywanie operacji administracyjnych.
Polecenia zarządzające uprawnieniami to:

::

   GRANT ALL PRIVILEGES ON DATABASE dbname TO username;
   REVOKE CONNECT ON DATABASE dbname FROM username;

Uprawnienia na poziomie bazy danych mogą być szczegółowo dostosowane do
potrzeb poszczególnych użytkowników lub grup użytkowników (ról). Na
przykład, można przyznać uprawnienia do:

-  Łączenia się z bazą danych (``CONNECT``).

-  Tworzenia nowych schematów (``CREATE``).

-  Wykonywania zapytań (``SELECT``) i modyfikacji danych (``INSERT``,
   ``UPDATE``, ``DELETE``).

-  Zarządzania tabelami i innymi obiektami bazy danych (``ALTER``,
   ``DROP``).

Poziom Tabeli
-------------

Na poziomie tabeli uprawnienia mogą obejmować selekcję, wstawianie,
aktualizację oraz usuwanie danych. Przykładowe polecenia to:

::

   GRANT SELECT, INSERT ON TABLE tablename TO username;
   REVOKE UPDATE ON TABLE tablename FROM username;

Precyzyjne zarządzanie uprawnieniami na poziomie tabeli pozwala na
ochronę danych przed nieautoryzowanym dostępem oraz modyfikacją.
Przykłady uprawnień obejmują:

-  ``SELECT`` - możliwość odczytu danych z tabeli.

-  ``INSERT`` - możliwość dodawania nowych rekordów do tabeli.

-  ``UPDATE`` - możliwość modyfikowania istniejących rekordów.

-  ``DELETE`` - możliwość usuwania rekordów.

Role i Grupy Użytkowników
-------------------------

PostgreSQL umożliwia tworzenie ról i grup użytkowników, co upraszcza
zarządzanie uprawnieniami. Role mogą mieć przypisane uprawnienia, które
są dziedziczone przez użytkowników przypisanych do tych ról. Przykładowe
polecenia:

::

   CREATE ROLE read_only;
   GRANT SELECT ON ALL TABLES IN SCHEMA public TO read_only;
   GRANT read_only TO username;

Stosowanie ról i grup użytkowników pozwala na bardziej elastyczne i
skalowalne zarządzanie uprawnieniami. Na przykład, można stworzyć rolę
``read_only``, która ma tylko uprawnienia do odczytu danych, a następnie
przypisać tę rolę wielu użytkownikom, co znacznie upraszcza
administrację.

.. _podsumowanie-1:

Podsumowanie
------------

Zarządzanie uprawnieniami użytkowników w PostgreSQL jest kluczowe dla
zapewnienia bezpieczeństwa i integralności danych. Dzięki możliwości
definiowania uprawnień na różnych poziomach (DBMS, baza danych, tabela)
oraz korzystaniu z ról i grup użytkowników, administratorzy mogą
precyzyjnie kontrolować dostęp do zasobów bazy danych.

Zarządzanie Użytkownikami a Dane Wprowadzone
============================================

Zarządzanie użytkownikami w PostgreSQL obejmuje tworzenie, modyfikowanie
i usuwanie użytkowników oraz ról. Ważnym aspektem jest zarządzanie
danymi wprowadzonymi przez użytkowników, szczególnie w kontekście
usuwania użytkowników.

Tworzenie i Modyfikowanie Użytkowników
--------------------------------------

Tworzenie nowych użytkowników w PostgreSQL odbywa się za pomocą
polecenia ``CREATE USER``. Przykład:

::

   CREATE USER username WITH PASSWORD 'password';

Modyfikowanie istniejących użytkowników można przeprowadzać za pomocą
polecenia ``ALTER USER``:

::

   ALTER USER username WITH PASSWORD 'new_password';

Usuwanie Użytkowników
---------------------

Usuwanie użytkowników w PostgreSQL odbywa się za pomocą polecenia
``DROP USER``. Przykład:

::

   DROP USER username;

Jednakże usunięcie użytkownika nie powoduje automatycznego usunięcia
danych, które zostały przez niego wprowadzone. Dane te pozostają w bazie
danych i mogą być dalej dostępne dla innych użytkowników z odpowiednimi
uprawnieniami.

Zachowanie Danych po Usunięciu Użytkownika
------------------------------------------

Dane wprowadzone przez usuniętego użytkownika pozostają w bazie danych,
co jest ważne dla zapewnienia integralności i ciągłości danych. W
praktyce oznacza to, że:

-  Rekordy w tabelach nadal istnieją i są dostępne dla innych
   użytkowników z odpowiednimi uprawnieniami.

-  Metadane, takie jak informacje o autorze danych, mogą być zachowane w
   celach audytowych.

Przykłady scenariuszy, w których zachowanie danych po usunięciu
użytkownika jest istotne:

-  **Zmiany kadrowe** - gdy pracownik odchodzi z firmy, jego dane
   powinny pozostać w systemie.

-  **Reorganizacja projektów** - dane wprowadzone przez użytkownika mogą
   być ważne dla trwających projektów.

-  **Naruszenia bezpieczeństwa** - w przypadku konieczności szybkiego
   usunięcia użytkownika, dane pozostają nienaruszone.

Polityki Retencji Danych
------------------------

Organizacje mogą wdrażać polityki retencji danych, które określają, jak
długo dane wprowadzone przez użytkowników są przechowywane oraz w jakich
warunkach mogą być usuwane. Polityki te mogą obejmować:

-  Automatyczne usuwanie danych po określonym czasie.

-  Przeglądy i audyty danych w celu określenia ich dalszej przydatności.

-  Mechanizmy archiwizacji danych w celu ich późniejszego odzyskania,
   jeśli zajdzie taka potrzeba.

.. _podsumowanie-2:

Podsumowanie
------------

Zarządzanie użytkownikami w PostgreSQL obejmuje nie tylko tworzenie i
usuwanie kont użytkowników, ale również zarządzanie danymi, które
zostały przez nich wprowadzone. Zachowanie tych danych po usunięciu
użytkownika jest kluczowe dla integralności systemu i może być
regulowane przez polityki retencji danych.

Zabezpieczenie Połączenia przez SSL/TLS
=======================================

SSL (Secure Sockets Layer) oraz TLS (Transport Layer Security) są
standardowymi technologiami zabezpieczającymi połączenia sieciowe, w tym
również połączenia z bazą danych PostgreSQL.

Konfiguracja SSL/TLS
--------------------

Aby włączyć SSL/TLS w PostgreSQL, należy skonfigurować plik
``postgresql.conf`` oraz odpowiednio dostosować plik ``pg_hba.conf``.
Przykład konfiguracji:

::

   # postgresql.conf
   ssl = on
   ssl_cert_file = 'server.crt'
   ssl_key_file = 'server.key'

Dodatkowo, w pliku ``pg_hba.conf`` należy zdefiniować reguły
uwierzytelniania z użyciem certyfikatów SSL:

::

   # pg_hba.conf
   hostssl all all 0.0.0.0/0 cert

Tworzenie i Zarządzanie Certyfikatami
-------------------------------------

Do korzystania z SSL/TLS konieczne jest posiadanie certyfikatu serwera
oraz klucza prywatnego. Certyfikaty te mogą być wydawane przez zaufane
urzędy certyfikacji (CA) lub generowane samodzielnie (self-signed).
Przykładowe polecenia do generowania własnych certyfikatów:

::

   openssl genrsa -des3 -out server.key 2048
   openssl req -new -key server.key -out server.csr
   openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt

Korzyści z SSL/TLS
------------------

SSL/TLS zapewnia szyfrowanie danych przesyłanych między klientem a
serwerem, co chroni przed podsłuchiwaniem oraz modyfikowaniem danych
podczas transmisji. Zapewnia również uwierzytelnienie serwera oraz,
opcjonalnie, klienta, co zwiększa bezpieczeństwo całego systemu.

Korzyści z używania SSL/TLS obejmują:

-  Ochronę danych wrażliwych podczas transmisji przez sieć.

-  Zapobieganie atakom typu man-in-the-middle, które polegają na
   przechwytywaniu i modyfikacji danych.

-  Uwierzytelnianie serwera, co pozwala klientom na weryfikację, że
   łączą się z właściwym serwerem.

Monitorowanie i Audyt Połączeń SSL/TLS
--------------------------------------

Ważnym aspektem korzystania z SSL/TLS jest monitorowanie i audyt
połączeń zabezpieczonych. PostgreSQL oferuje mechanizmy logowania, które
mogą rejestrować informacje o połączeniach SSL/TLS, co pozwala na:

-  Identyfikację prób nieautoryzowanego dostępu.

-  Analizę i diagnostykę problemów z połączeniami.

-  Zapewnienie zgodności z politykami bezpieczeństwa organizacji.

.. _podsumowanie-3:

Podsumowanie
------------

SSL/TLS jest kluczowym elementem zabezpieczania połączeń w PostgreSQL.
Dzięki szyfrowaniu danych podczas transmisji oraz uwierzytelnianiu
serwera i klienta, SSL/TLS znacząco podnosi poziom bezpieczeństwa bazy
danych.

Szyfrowanie Danych
==================

Szyfrowanie danych w PostgreSQL może odbywać się zarówno na poziomie
transmisji danych, jak i na poziomie przechowywania danych.

Szyfrowanie w Transmisji
------------------------

Jak wspomniano wcześniej, SSL/TLS umożliwia szyfrowanie danych podczas
transmisji między klientem a serwerem, co zapobiega nieautoryzowanemu
dostępowi do danych w trakcie ich przesyłania.

Szyfrowanie na Poziomie Dysku
-----------------------------

PostgreSQL nie posiada natywnego wsparcia dla szyfrowania danych na
poziomie tabel lub baz danych, jednak możliwe jest wykorzystanie
zewnętrznych narzędzi i systemów plików szyfrujących. Przykładem może
być system plików z szyfrowaniem (np. LUKS w systemach Linux) lub
szyfrowanie oferowane przez rozwiązania chmurowe (np. Amazon RDS).

Przykładowa konfiguracja szyfrowania dysku na systemie Linux z użyciem
LUKS:

::

   sudo cryptsetup luksFormat /dev/sdX
   sudo cryptsetup luksOpen /dev/sdX encrypted_disk
   sudo mkfs.ext4 /dev/mapper/encrypted_disk
   sudo mount /dev/mapper/encrypted_disk /mnt/encrypted

Szyfrowanie na Poziomie Aplikacji
---------------------------------

Innym podejściem do szyfrowania danych jest szyfrowanie na poziomie
aplikacji, gdzie dane są szyfrowane przed zapisaniem do bazy danych i
odszyfrowywane po ich odczytaniu. Takie podejście zapewnia pełną
kontrolę nad procesem szyfrowania, jednak wymaga dodatkowej
implementacji w kodzie aplikacji.

Przykładowe biblioteki do szyfrowania danych na poziomie aplikacji:

-  **Python** - ``cryptography``, ``pycryptodome``.

-  **Java** - ``javax.crypto``, ``Bouncy Castle``.

-  **JavaScript** - ``crypto``, ``sjcl``.

Zarządzanie Kluczami Szyfrującymi
---------------------------------

Kluczowym elementem skutecznego szyfrowania danych jest zarządzanie
kluczami szyfrującymi. Klucze muszą być bezpiecznie przechowywane i
zarządzane, aby zapobiec ich utracie lub kradzieży. Przykładowe
narzędzia do zarządzania kluczami:

-  **HashiCorp Vault** - bezpieczne przechowywanie i zarządzanie
   tajemnicami oraz kluczami szyfrującymi.

-  **AWS Key Management Service (KMS)** - zarządzanie kluczami w
   środowisku chmurowym Amazon Web Services.

-  **GCP Cloud KMS** - zarządzanie kluczami w środowisku Google Cloud
   Platform.

.. _podsumowanie-4:

Podsumowanie
------------

Szyfrowanie danych jest kluczowym elementem zabezpieczenia baz danych
PostgreSQL. Może ono odbywać się na różnych poziomach, od transmisji
danych po przechowywanie na dysku. Skuteczne szyfrowanie wymaga
odpowiedniego zarządzania kluczami szyfrującymi oraz integracji z
systemami plików lub aplikacjami.

.. _podsumowanie-5:

Podsumowanie
============

Bezpieczeństwo w PostgreSQL obejmuje szeroki zakres zagadnień, od
zarządzania dostępem i uprawnieniami użytkowników, przez zabezpieczenie
połączeń, aż po szyfrowanie danych. Plik ``pg_hba.conf`` oraz mechanizmy
uwierzytelniania odgrywają kluczową rolę w kontroli dostępu do bazy
danych. Zarządzanie uprawnieniami na różnych poziomach pozwala na
precyzyjną kontrolę nad tym, kto i jakie operacje może wykonywać.
Zabezpieczenie połączeń przez SSL/TLS oraz szyfrowanie danych są
nieodzowne dla ochrony przed nieautoryzowanym dostępem i zapewnienia
integralności danych. Dzięki tym mechanizmom PostgreSQL może być
bezpiecznie używany nawet w najbardziej wymagających środowiskach.
    """

.. :Indices and tables
==================

.. * :ref:`genindex`
.. * :ref:`modindex`
.. * :ref:`search`
