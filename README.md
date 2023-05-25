# Instalacja IBM Spectrum Protect 

W ramach instalacji przeprowadzane playbook wykonuje następujące kroki:

1. Zakłada LUNy na macierzy.
1. Skanuje urządzenia na AIXie 
1. Przygotowuje OS do roli serwera Spectrum Protect
	1. Zakłada użytkownika instancji
	1. Zakłada filesystemy 

## Materiały pomocnicze:

- Dokumentacja modułu [ibm.power_aix](https://ibm.github.io/ansible-power-aix/index.html)
- Dokumentacja modułu [ibm.spectum_virtualize]()

# Przygotowanie środowiska

## Ansible host

Host, który będzie uruchamiał playbook, musi spełniać nastęþujące wymagania

- Dostęp, najlepiej po kluczach do zarządzanych systemów.
- Posiadać zainstalowane moduły :
	- `ibm.power_aix`

	```
	❯ ansible-galaxy collection install -f ibm.power_aix
	Starting galaxy collection install process
	Process install dependency map
	Starting collection install process
	Downloading https://galaxy.ansible.com/download/ibm-power_aix-1.6.1.tar.gz to /home/marcinek/.ansible/tmp/ansible-local-513942m0tz9n9/tmpj3peeea4/ibm-power_aix-1.6.1-m3clxxf_
	Installing 'ibm.power_aix:1.6.1' to '/home/marcinek/.ansible/collections/ansible_collections/ibm/power_aix'
	ibm.power_aix:1.6.1 was installed successfully
	```

	- `ibm.spectrum_virtualize`

	```
	❯ ansible-galaxy collection install -f ibm.spectrum_virtualize
	Starting galaxy collection install process
	Process install dependency map
	Starting collection install process
	Downloading https://galaxy.ansible.com/download/ibm-spectrum_virtualize-1.12.0.tar.gz to /home/marcinek/.ansible/tmp/ansible-local-516915yry1_2s/tmpiyg_z4qb/ibm-spectrum_virtualize-1.12.0-tmpyehcg
	Installing 'ibm.spectrum_virtualize:1.12.0' to '/home/marcinek/.ansible/collections/ansible_collections/ibm/spectrum_virtualize'
	ibm.spectrum_virtualize:1.12.0 was installed successfully
	```

## AIX

System musi spełniać wymagania opisane w [dokumentacji kolekcji](https://ibm.github.io/ansible-power-aix/requirements.html). W skrócie:

	- AIX 7.1 lub wyższy
	- Python 3. Im nowszy tym lepszy.

## Macierz 

# Playbook `aix-restore.yaml`

To jest playbook pomocniczy, do resetowania środowiska. Jego funkcjie to:

- Uruchomić relację flash copy podaną w zmiennej `{{ os_res_fcmap }}`

*Plan, żeby było ładnie:*

- Gasi na miekko AIXy z grupy `[all-aix]`.
- Upewnia się na HMC, że LPARY leżą
- Odwraca replikację startując `{{ os_res_fcmap }}`
- Zdejmuje mapowania LUNów z listy `{{ sp_luns }}`
- Kasuje LUNy z listy  `{{ sp_luns }}`