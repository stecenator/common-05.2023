# Plan prezentacji

Planowany przebieg prelekcji. 

# Powtórka z Ansibla

Struktura playbooka:

- Potrzebne kolekcje
- play
- role
- struktura katalogów
- zmienne
- sekrety


## Kolekcje

Do poprawnego działania potrzebne są następujące kolekcje z repozytoriów IBM:

- `ibm.spectrum_virtualize`
- `ibm.power_aix`
- `ibm.power_hmc`

### Instalacja potrzebych kolekcji

1. Zainstaluj powyższe moduły:

	```
	❯ ansible-galaxy collection install -f ibm.power_aix
	Starting galaxy collection install process
	Process install dependency map
	Starting collection install process
	Downloading https://galaxy.ansible.com/download/ibm-power_aix-1.6.1.tar.gz to /home/marcinek/.ansible/tmp/ansible-local-513942m0tz9n9/tmpj3peeea4/ibm-power_aix-1.6.1-m3clxxf_
	Installing 'ibm.power_aix:1.6.1' to '/home/marcinek/.ansible/collections/ansible_collections/ibm/power_aix'
	ibm.power_aix:1.6.1 was installed successfully
	
	❯ ansible-galaxy collection install -f ibm.spectrum_virtualize
	Starting galaxy collection install process
	Process install dependency map
	Starting collection install process
	Downloading https://galaxy.ansible.com/download/ibm-spectrum_virtualize-1.12.0.tar.gz to /home/marcinek/.ansible/tmp/ansible-local-516915yry1_2s/tmpiyg_z4qb/ibm-spectrum_virtualize-1.12.0-tmpyehcg
	Installing 'ibm.spectrum_virtualize:1.12.0' to '/home/marcinek/.ansible/collections/ansible_collections/ibm/spectrum_virtualize'
	ibm.spectrum_virtualize:1.12.0 was installed successfully

	❯ ansible-galaxy collection install -f ibm.power_hmc
	Starting galaxy collection install process
	Process install dependency map
	[...]
	```

1. Wyświetl zainstalowane kolekcje:

	```
	❯ ansible-galaxy collection list

	# /home/marcinek/.ansible/collections/ansible_collections
	Collection              Version
	----------------------- -------
	ansible.netcommon       1.4.1  
	ansible.posix           1.1.1  
	community.general       1.3.0  
	community.kubernetes    1.1.1  
	community.vmware        1.11.0 
	google.cloud            1.0.1  
	ibm.power_aix           1.6.1  
	ibm.power_hmc           1.6.0  
	ibm.power_ibmi          1.1.2  
	ibm.spectrum_virtualize 1.12.0 

	# /usr/lib/python3.11/site-packages/ansible_collections
	Collection                    Version
	----------------------------- -------
	amazon.aws                    5.4.0  
	ansible.netcommon             4.1.0  
	ansible.posix                 1.5.2  
	[...]
	```

1. Wyświetl listę modułów w kolekcji

	```
	❯ ansible-doc -l ibm.power_aix
	ibm.power_aix.aixpert                         System security settings management                                                       
	ibm.power_aix.alt_disk                        Alternate rootvg disk management                                                          
	ibm.power_aix.backup                          Data or system volume group backup management                                             
	ibm.power_aix.bootlist                        Alters the list of boot devices available to the system                                   
	ibm.power_aix.bosboot                         Creates boot image                                                                        
	ibm.power_aix.chsec                           Modify AIX stanza files                                                                   
	ibm.power_aix.devices                         Devices management                                                                        
	ibm.power_aix.emgr                            System interim fixes management                                                           
	ibm.power_aix.filesystem                      Local and NFS filesystems management                                                      
	ibm.power_aix.flrtvc                          Generate FLRTVC report, download and install security and HIPER fixes 
	```

1. Szczegóły modułu:

	```
	❯ ansible-doc ibm.power_aix.lvg
	> IBM.POWER_AIX.LVG    (/home/marcinek/.ansible/collections/ansible_collections/ibm/power_aix/plugins/modules/lvg.py)

	        This module creates, removes, modifies attributes, resizes, activates and deactivates volume groups.

	ADDED IN: version 0.4.0 of ibm.power_aix

	OPTIONS (= is mandatory):

	- auto_on
	        Specifies that the volume group is automatically available during a system restart.
	        By default `auto_on=yes' when creating a volume group.
	        Can be used when creating or changing a volume group, hence when `state=present'.
	        default: null
	        type: bool
	[...]
    ```

## Sekrety

Dobrze jest nie trzymać haseł, nawet w formie hashy bezpośrednio w playbooku. Podejśc jest kilka, ja wybrałem `ansible-vault`, który plik z krytycznymi zmienymi potrafi po prostu zaszyfrować. Szczególowy opis znajduje się [tutaj](https://www.redhat.com/sysadmin/ansible-playbooks-secrets). Całość sprowadza się do następującycj kroków:

1. Utwórz tekstowy plik `secrets/secrets.enc`  (katalog i nazwa dowolne). Wrzuć tam swoje zmienne:

	```
	sp_pass: "C0mm0n2023"
	```
2. Zaszyfruj go 

	```
	❯ ansible-vault encrypt secrets.enc
	New Vault password: 
	Confirm New Vault password: 
	Encryption successful
	❯ cat secrets.enc
	$ANSIBLE_VAULT;1.1;AES256
	61653236363337633534656533353332626234346233336630633264323862663663356263396463
	[...]
	```
3. Wołaj playbooka z opcjami jak poniżej:

	```
	❯ ansible-playbook brudnopis.yaml -e @secrets/secrets.enc --ask-vault-pass
	Vault password: 
	```
4. Chcesz coś zmienić w zaszyfrowanym pliku? Nic prostszego:

	```
	❯ ansible-vault edit secrets/secrets.enc
	Vault password: 
	```

	Po podaniu hasła wywoła się `$EDITOR` z Twoim plikiem. Po zamknięciu, plik zotanie automatycznie zaszyfrowany z powrotem.

# Instalacja!

Instalację rozpoczynamy mając następujące środowisko:

- AIXa z wymienionionego kluczami ssh root'a z kontrolerem Ansible. Host `ajax.iic` w inventory.
- AIX jest postawiony na macierzy określonej zmienną `{{ fs_cluster }}` w `group_vars/storage.yaml`.
- Na wypadek pomyłki lub chęci puszczenia wszystkiego od 0, AIX jest zesnapshotowany i istnieje mapowanie określone zmienną `{{ os_res_fcmap }}` w `group_vars/storage.yaml`. Jeśli chcesz **wszystko** odkręcić, po prostu puść:

	```
	❯ ansible-playbook AIX-restore.yaml -e @secrets/secrets.enc --ask-vault-pass
	```

Całość można oczywiście umieścić w jednym playbooku, ale do celów edukacyjnych podzieliłem go na dwie cześći, a taski otagowałem, żeby można je było puszczać pojedynczo.

## Pamięc masowa

Ponieważ AIX jest już gotowy, to znaczy: 

- jest LPAR
- jest zoning
- jest LUN z zainstalowanym OS
- jet wjazd po ssh 

(oczywiście wszystko to, da się zrobić Ansiblem :-) ), to można zacząć od przygotowania pamięci masowej dla IBM Spectrum Protect. Opis tego storage jest zawarty w zmiennej `{{ sp_luns }}` zdefiniowanej w w `group_vars/storage.yaml`

```
sp_luns:                # Hash w postaci Nawa_lunu: rozmiar  (w GiB)
  COMMON_spinst1: 20
  COMMON_actlog: 132
  COMMON_archlog: 256
  COMMON_db01: 64
  COMMON_db02: 64
  COMMON_dbb: 256
  COMMON_dc01: 64
  COMMON_dc02: 64
```

Można zatem uruchomić przygotowanie LUNów i zamapowanie ich na naszego AIXa:

```
❯ ansible-playbook   -e @secrets/secrets.enc --ask-vault-pass prep_storage.yaml
```

## AIX - przygotowanie OS do roli SP

### Użyszkodnik instancji `{{ sp_user }}`

Standard AIX to sporo za mało:
```
$ ulimit -a 
time(seconds)        unlimited
file(blocks)         2097151
data(kbytes)         131072
stack(kbytes)        32768
memory(kbytes)       32768
coredump(blocks)     2097151
nofiles(descriptors) 2000
threads(per process) unlimited
processes(per user)  128

```

Dlatego zmieniamy według zaleceń [IBM Spectrum Protect](https://www.ibm.com/docs/en/spectrum-protect/8.1.18?topic=instance-verifying-access-rights-user-limits)

```
Wrzucić sp_user_attrs
```

Dzięki temu `ulimit'y` użytkownika instancji bedą wyglądać tak:

```
$ ulimit -a
time(seconds)        unlimited
file(blocks)         unlimited
data(kbytes)         131072
stack(kbytes)        32768
memory(kbytes)       32768
coredump(blocks)     unlimited
nofiles(descriptors) 65536
threads(per process) unlimited
processes(per user)  16384
``` 

## Instalacja SP