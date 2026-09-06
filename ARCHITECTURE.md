# [Architecture](https://github.com/scribe-org/Scribe-Data/blob/main/ARCHITECTURE.md)

This markdown file documents the architecture for the Scribe-Data CLI - including all processes and the external systems and sources with which it interacts. The diagram details the CLI [convert](./src/scribe_data/cli/convert/), [download](./src/scribe_data/cli/download/), [get](./src/scribe_data/cli/get.py), [list](./src/scribe_data/cli/list/) and [total](./src/scribe_data/cli/total/) commands, with [interactive](./src/scribe_data/cli/interactive/) being a command itself and also an option within other commands via the `--interactive` (`-i`) option.

CLI outputs that are used in multiple flows appear as nodes outside of any nodes for clarity. As the file is meant to be a living document, edits are welcome to expand and update it!

> [!NOTE]
> You can see the architecture diagram for all of [Scribe](https://github.com/scribe-org) [here](https://github.com/scribe-org/Organization/blob/main/ARCHITECTURE.md).

## Architecture Diagram

```mermaid
graph LR
    %% CLI

    DATA[[Scribe-Data CLI]]

    %% Data sources

    WD[(Wikidata lexemes)]
    WK[(Wiktionary translations)]
    UNI((Unicode emojis))

    %% Outputs

    JSON(JSON files)
    CTSV(CSV / TSV files)
    SQLITE(SQLITE DB)
    TERM(Terminal output)
    WDDUMP(Wikidata lexeme dump)
    WKDUMP(Wiktionary dump)

    %% Commands

    DLWD{{Wikidata\ndownload command}}
    DLWK{{Wiktionary\ndownload command}}
    LIST{{list command}}
    GET{{get command}}
    TOT{{total command}}
    CONV{{convert command}}
    INT{{interactive mode}}

    %% General flow

    WD ---> |dump download| DLWD
    WK ---> |dump download| DLWK

    DLWD --> |Saved locally| WDDUMP
    DLWK --> |Saved locally| WKDUMP

    WD ---> |Wikidata SPARQL\nquery service| GET
    WDDUMP ---> |Wikidata\ndump parse| GET
    WKDUMP ---> |Wiktionary\ndump parse| GET

    WD ---> |Wikidata SPARQL\nquery service| TOT
    WDDUMP ---> |Wikidata\ndump parse| TOT
    WKDUMP ---> |Wiktionary\ndump parse| TOT

    GET ---> |Saved locally| JSON
    JSON ---> |user wants\ndifferent format| CONVERT_FLOW

    LIST ---> |Print output| TERM
    TOT ---> |Print output| TERM

    %% Subgraphs

    subgraph DOWNLOAD_FLOW [download flow]
    DLWD
    DLWK
    end

    subgraph GET_FLOW [get flow]
    UNI ---> |Derive emojis\nfrom included files| GET
    end

    subgraph CONVERT_FLOW [convert flow]
    CONV ---> |Local files converted| CTSV
    CONV ---> |Local files converted| SQLITE
    end

    subgraph TOTAL_FLOW [total flow]
    TOT
    end

    subgraph LIST_FLOW [list flow]
    DATA ---> |CLI internal\ndata read| LIST
    end
```

> [!NOTE]
> The architecture diagram above was created using the diagramming tool [Mermaid](https://github.com/mermaid-js/mermaid), with rendering supported in GitHub markdown.
