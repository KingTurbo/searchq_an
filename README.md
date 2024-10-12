# searchq_an
# Google-Abfrage (Data-Collecting)
1. **API-Aufruf**: Google Suggest API über die URL `https://suggestqueries.google.com/complete/search?output=toolbar&hl=de&q=schärding` für deutsche Ergebnisse (URL von "en" auf "de" geändert).
    
2. **Erweiterte Suchbegriffe**: Nutzung von `expanded_term_prefixes` (Array), ins Deutsche übersetzt.
	1. Diese werden an provided Keyword angehängt und api wird erneut abgerufen
3. **Buchstabenabfrage**: Die Suchbegriffe werden mit jedem Buchstaben des Alphabets erweitert, um alle möglichen Vorschläge zu erfassen.
    1. Diese werden an provided Keyword angehängt und api wird erneut abgerufen
	    Bsp. "Schärding a"; "Schärding b"
	    
1. **Antwortverarbeitung**: API gibt Ergebnisse im JSON-Format zurück, einschließlich Relevanzwerte (`suggestrelevance`). Direkt von Google selbst.
    
5. **Datenaufbereitung**: Die JSON-Ergebnisse aller Abfragen werden extrahiert und in ein Pandas DataFrame umgewandelt. 

Die Library "ecommercetools" funktioniert so nicht mehr, allerdings die einzelnen Code Snippets schon. Random User-Agents Wechsel wurde erweitert. 

# Search-Results Analyse:

eyewear_keywords_de beinhaltet keywords, die passend zum Thema "Brille" selektiert wurden. Diese durchlaufen alle die vorher erwähnten Schritte.

Aus den Resulaten werden Fragewörter und das Keyword selbst entfernt. Grund: Wenn keyword nicht gelöscht wird, wird nach bestimmter n-Anzahl nur nach dem keyword selbst gruppiert (was nicht sinn und zweck ist). Selbiges mit den Fragenwörter ("wie", "was", usw.) = Man könnte zwar aus api löschen, würde aber zu Qualitätsverlust führen,

Alle suggestions df werden in eine Liste gepackt und zusammengefügt.

UMAP und HDBSCAN sind beides clustering algorythmen. 
("all-MiniLM-L6-v2") = Text in numerische Vektoren

CountVectorizer(ngram_range=(1, 2), stop_words=stopwords) zählt Häufigkeit von Wörtern in einer search query

BERTopic wird dann mit den einzelnen Modellen gefüllt und berechnet. 


# Finales Modell:
{'calculate_probabilities': True,
 'ctfidf_model': ClassTfidfTransformer(),
 'embedding_model': <bertopic.backend._sentencetransformers.SentenceTransformerBackend at 0x7cbbcaf86770>,
 'hdbscan_model': HDBSCAN(gen_min_span_tree=True, min_cluster_size=10, min_samples=5,
         prediction_data=True),
 'language': None,
 'low_memory': False,
 'min_topic_size': 10,
 'n_gram_range': (1, 1),
 'nr_topics': None,
 'representation_model': None,
 'seed_topic_list': None,
 'top_n_words': 3,
 'umap_model': UMAP(min_dist=0.03, n_components=3, n_neighbors=3, tqdm_kwds={'bar_format': '{desc}: {percentage:3.0f}%| {bar} {n_fmt}/{total_fmt} [{elapsed}]', 'desc': 'Epochs completed', 'disable': True}),
 'vectorizer_model': CountVectorizer(ngram_range=(1, 2),
                 stop_words=['aber', 'alle', 'allem', 'allen', 'aller', 'alles',
                             'als', 'also', 'am', 'an', 'ander', 'andere',
                             'anderem', 'anderen', 'anderer', 'anderes',
                             'anderm', 'andern', 'anderr', 'anders', 'auch',
                             'auf', 'aus', 'bei', 'bin', 'bis', 'bist', 'da',
                             'damit', 'dann', ...]),
 'verbose': True,
 'zeroshot_min_similarity': 0.7,
 'zeroshot_topic_list': None}

Mit diesen Parametern wurde mit data4 das beste Ergebnis erzielt.
Entscheident dabei war,: (min_cluster_size=10, min_samples=5) und nur search request mit dem keywort "brille" alleine

