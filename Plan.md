# Plan prezentacji

## Powtórka z Ansibla

Struktura playbooka:

- play
- role
- struktura katalogów
- zmienne

## Moduły AIX

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


## Moduły SVC

## Pamięc masowa

## AIX - przygotowanie OS do roli SP

## Instalacja SP