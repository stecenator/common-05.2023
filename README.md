# Instalacja IBM Spectrum Protect 

W ramach instalacji przeprowadzane playbook wykonuje następujące kroki:

1. Zakłada LUNy na macierzy.
1. Skanuje urządzenia na AIXie 
1. Przygotowuje OS do roli serwera Spectrum Protect
	1. Zakłada użytkownika instancji
	1. Zakłada filesystemy 

## Materiały pomocnicze:

- Dokumentacja modułu [ibm.power_aix](https://ibm.github.io/ansible-power-aix/index.html)
- Dokumentacja modułu [ibm.power_hmc](https://ibm.github.io/ansible-power-hmc/index.html)
- Dokumentacja modułu [ibm.spectum_virtualize](https://docs.ansible.com/ansible/latest/collections/ibm/spectrum_virtualize/index.html)

# Przygotowanie środowiska

## Ansible host

Host, który będzie uruchamiał playbook, musi spełniać nastęþujące wymagania

- Dostęp, najlepiej po kluczach do zarządzanych systemów.


## AIX

System musi spełniać wymagania opisane w [dokumentacji kolekcji](https://ibm.github.io/ansible-power-aix/requirements.html). W skrócie:

	- AIX 7.1 lub wyższy
	- Python 3. Im nowszy tym lepszy. Ale wmurowany 2.7 też się nadaje!

## Macierz 

Kolekcja `ibm.spectrum_virtulize` bazuje na REST API. To znaczy, że moduły są wołane z hosta kontrolnego, a nie zdalnie. Szczegółowe wymagania oraz instalacja opisane są [tutaj](https://github.com/ansible-collections/ibm.spectrum_virtualize), ale sprowadzają się do:

- dostępu do REST API macierzy: https://adres.macierzy:7443/rest 
- Firmware macierzy > 8.3.1.5.

# Zmienne

Wszystkie playbooki zostały napisane tak, żeby ułatwić ich użycie w warunkach "bojowych", to znaczy łatwo je dostosować poprzez zmianę wartości poszczególnych zmiennych

Zmienne z grupy `[storage]`:

- **`fs_cluster`** - Adres macierzy na której będą zakładane LUNy
- **`fs_user`** - Konto administratora macierzy.
- **`fs_log`** - Plik diagnostyczny modułów kolekcji `ibm.spectrum_virtualize`
- **`sp_luns`** - Hash w postaci par `nazwa_lunu:rozmiar_w_GiB` - lista LUNów do manipulowania przez playbooki związane z obsługą pamięci masowej.


# Playbook `AIX-restore.yaml`

To jest playbook pomocniczy, do resetowania środowiska. Jego funkcjie to:

- Gasi na miekko AIXa z wskazanego na pugle `{{ sys_name }}` w LPARze `{{ lpar_name }}`.
- Upewnia się na HMC, że LPAR leży. **Update:** ten krok okazał się zbędny, bo moduł gaszący LPAR czeka aż ten zgaśnie. Ale zostawiam ten kod jako przykład klauzuli `until` w tasku.
- Odwraca replikację startując `{{ os_res_fcmap }}`
- Zdejmuje mapowania LUNów z listy `{{ sp_luns }}`
- Kasuje LUNy z listy  `{{ sp_luns }}`
- Startuje czystego AIXa z powrotem. 

# Playbook `prep_storage.yaml`

Przygotowuje LUNy i je mapuje na hosta AIXowego.



# Playbook `AIX-spinstall.yaml`

Przygotowuje OS do instalacji i konfiguracji IBM Spectrum Protect:

- Zakłada usera instancji, wraz zrekomendowynymi ustawieniami.
- Skanuje storage w poszukiwaniu dysków do założenia filesystemów.
- Zakłada filesystemy pod instancję Spectrum Protect.
- Instaluje w trybie *silent* IBM Spectrum Protectc
- Konfiguruje instancję IBM Spectrum Protect

## Zmienne

