# Plan prezentacji

## Powtórka z Ansibla

Struktura playbooka:

- play
- role
- struktura katalogów
- zmienne
- sekrety

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

## Zainstalowane kolekcje

Żeby sprawdzić zainstalowane w systemie oraz na Twoim koncie kolekcje Ansible wykonaj polecenie:

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

## Kolekcja AIX

### Komenda `ansible-doc` w akcji:

1. Wyświetlenie listy modułów w kolekcji

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


## Kolekcja SVC

Analogicznie do modułów AIX, wyświetl 
1. 

## Pamięc masowa

## AIX - przygotowanie OS do roli SP

## Instalacja SP