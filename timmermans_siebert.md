# Evaluatie Enterprise Linux

| :---          | :---                                                |
| Student       | Siebert TIMMERMANS                                  |
| Klasgroep     | TIN-TI-3B                                           |
| Email         | <mailto:siebert.timmermans.x7889@student.hogent.be> |
| Hoofdopdracht | SME                                                 |
| Repo          | <https://github.com/HoGentTIN/elnx-sme-SiebertT>    |

## Hoofdopdracht

- W4:
    - Fout bij toepassen mariadb-rol, host_vars bevatte

        ```
        vars:
          wordpress_databases:
          [...]
        ```

    - Labo 00 afgewerkt, testrapport is ok
    - Bezig met labo 1 (LAMP), aantal fouten er uit gehaal. Nog te doen: servercertificaat
- W5:
    - Labo 01 afgewerkt, TODO is nog automatiseren installatie servercertificaat
    - Setup voor DNS-server ok: `vagrant-hosts.yml`, `site.yml`, `host_vars/pu001.yml`
- W9:
    - Actualiteitsopdracht getoond (zie verder)
    - Labo 02 (DNS) afgewerkt, is prima
    - Cheat sheet getoond, is ok, maar wel toegespitst op de labo's
- W10:
    - Samba probleem -> gebruiken nog `el7` rol (op basis van documentatie `bertvv.samba`). Gebruik de nieuwe `rhbase` rol. Ook: geen specifieke rechten per gebruiker toekennen, enkel op basis van groepen

### Eindbeoordeling

- Fileserver: ok
    - beschikbaar vanop hostsysteem
- DHCP-configuratie:
    - DHCP-server is bereikbaar, aangetoond via `nmap`
    - Niet helemaal correct:
        - Host-declaraties in ander subnet
        - DNS-servers: eigen autoritative servers gebruikt, d.w.z. dat werkstations enkel binnen het eigen netwerk kunnen surfen, nooit naar buiten
- Router niet gerealiseerd
- Werkstation niet gerealiseerd

O1: Nog niet bekwaam

## Troubleshooting

### Eerste troubleshooting-opdracht

- demo: 12.30 nginx up, http timeout
- Netwerklaag:
    - kan je pingen ts host en VM?
- Transportlaag:
    - Heb je listener voor 443 *toegevoegd* of die voor 8443 *aangepast*? Dat is niet duidelijk.
    - Niet gecontroleerd op open poorten met `ss -tlnp`
    - Firewall: ofwel `--add-service`, ofwel `--add-port`, maar niet beide
- Applicatielaag: resultaten van je tests??
- Eindresultaat niet beschreven

Beoordeling voor deze opdracht: "Nog niet bekwaam" omdat niet aangetoond is dat de service bereikbaar was vanop het hostsysteem en omdat het verslag onvolledig is.

### Tweede troubleshooting-opdracht

- Transportlaag:
    - Verwarring terminologie? "luisteren" betekent open socket, heeft niets met firewall te maken
    - Gebruik `ss` in plaats van `netstat` (= deprecated)
- Applicatielaag:
    - Je ondervraagt de verkeerde DNS-server (die van HoGent). Leer je tools gebruiken!
    - `listen-on` -> `any`!
    - 6/8 tests slagen

Eigenlijk is er niet aangetoond dat je de DNS-server vanaf het hostsysteem kan ondervragen, maar dit komt volgens mij enkel omdat je `nslookup` niet correct kan gebruiken. Voor zover ik kan zien in het verslag zou dit in principe moeten lukken.

Beoordeling: Bekwaam

### Eindbeoordeling

O2: Bekwaam

## Opdracht Actualiteit

Ubuntu-server op Azure met desktop-omgeving die je via RDP kan aanspreken

- Gedetailleerd stappenplan voor aanmaken, configuratie security settings
- Demo getoond, MATE desktop die op Azure draait

### Eindbeoordeling

O3: Deskundig

## Rapportering

### Laboverslagen

- Gebruik Markdown "Fenced code blocks", geen screenshots!
- Het is niet nodig om code te herhalen in verslag. Nadruk op testen!
- Gedetailleerde verslagen, testplan/rapport

R1: Deskundig

### Demonstraties

R2: Gevorderd

### Cheat sheet

- Aangevuld met eigen zaken
- Vrij specifiek voor de labo's, niet algemeen bruikbaar.

R3: Bekwaam

