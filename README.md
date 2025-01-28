# ZES Unica - Provvedimenti di autorizzazione

![](ZES.png)

Elenco dei provvedimenti di autorizzazione unica rilasciati dalla Struttura di missione ZES ai sensi degli articoli 14 e 15 del decreto-legge n. 124 del 2023.
#opendata estratti dai [PDF messi a disposizione del cittadino](https://www.strutturazes.gov.it/it/sportello-unico/provvedimenti-di-autorizzazione/) e trasformati in csv con script #Python

---

```{python}
import camelot
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt	
import pandas as pd
import geopandas as gpd # pip install geopandas
pdf_path = 'D:/au-24012025.pdf' # Path to the PDF

tables = camelot.read_pdf(pdf_path, pages="all", strip_text="\n", line_scale=40) # Extract tables using Camelot
df = pd.concat([table.df for table in tables], ignore_index=True) # Combine all extracted tables into a single DataFrame
df = df[2:].reset_index(drop=True) # Drop the first two rows
df.columns = ["Numero AU", "Data protocollo", "ID pratica", "Tipologia di intervento", "Comune", "Provincia"]
df["Numero AU"] = df["Numero AU"].astype(int)  # Converte in intero
df["Data protocollo"] = pd.to_datetime(df["Data protocollo"], format="%d/%m/%Y")  # Converte in data

# Funzione per dividere partendo dall'ultima parte
def split_id_pratica(value):
    parts = value.split("-")
    id_value = parts[-1] if len(parts) >= 1 else None
    in_value = pd.to_datetime(parts[-2], format="%d%m%Y", errors="coerce") if len(parts) >= 2 else None
    cf_value = parts[-3] if len(parts) >= 3 else None
    return cf_value, in_value, id_value

# Applicazione della funzione e creazione delle nuove colonne
df[["CF", "IN", "ID"]] = df["ID pratica"].apply(split_id_pratica).apply(pd.Series)

df.to_csv('D:/ZES_2025.csv',sep='|')

```
