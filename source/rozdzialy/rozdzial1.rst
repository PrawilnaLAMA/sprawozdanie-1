1. pg_hba.conf - Sterowanie Dostępem
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
