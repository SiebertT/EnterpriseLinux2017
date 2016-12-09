# Evaluatie Enterprise Linux

| :---          | :---                     |
| Student       | Siebert TIMMERMANS |
| Klasgroep     | TIN-TI-3B                  |
| Email         | <mailto:siebert.timmermans.x7889@student.hogent.be>         |
| Hoofdopdracht | SME               |
| Repo          | <https://github.com/HoGentTIN/elnx-sme-SiebertT>                 |

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

O1: <BEOORDELING>

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

### Eindbeoordeling

O2: <BEOORDELING>

## Opdracht Actualiteit

Ubuntu-server op Azure met desktop-omgeving die je via RDP kan aanspreken

- Gedetailleerd stappenplan voor aanmaken, configuratie security settings
- Demo getoond, MATE desktop die op Azure draait

Normaal is Azure h

### Eindbeoordeling

O3: <BEOORDELING>

## Rapportering

### Laboverslagen

R1: <BEOORDELING>

### Demonstraties

R2: <BEOORDELING>

### Cheat sheet

R1: <BEOORDELING>

