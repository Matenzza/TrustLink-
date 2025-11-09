# TrustLink-
plateforme d’analyse et de détection de liens malveillants conçue pour protéger les utilisateurs et les entreprises contre les menaces en ligne telles que le phishing, les malwares, et les sites frauduleux.


Explication claire et complète de ce que fait maintenant le script

Vue d'ensemble

phish_core.analyze_site(url) orchestre toute l'analyse et renvoie un objet JSON structuré contenant :
meta (timestamp, target)
url_analysis (lexical)
tls (certificate info)
dns (resolved IPs)
whois (registrant / dates, si python-whois installé)
http_response_headers + security_headers (résultat d'une requête statique)
html_analysis (statique, forms, JS suspects, tokens)
dynamic_analysis (résultat Playwright : final_url, redirections, requêtes/headers, téléchargements, permissions demandées, screenshot path)
virustotal (intégration via API v3 si clé)
urlscan (scan + report if key)
safe_browsing (Google Safe Browsing, optionnel si clé)
analysis (heuristic scoring, % risk, label et raisonnements détaillés)
Ce que le script couvre par checklist (mappage direct)

Structure du site (HTML/JS) : oui — analyse statique des scripts inline/externes, détection d'obfuscation (eval, atob, longues chaînes base64, hex escapes), formulaires et ratio de liens externes.
Domaine et certificat : oui — WHOIS (age du domaine) si whois installé, TLS certificate parsing (notBefore/notAfter/issuer/expired).
Réseau : partiellement — dynamic_analysis capture les requêtes et réponses (headers, status), détecte websockets, agrège domaines externes, identifie requêtes vers IPs et auto-downloads ; pour géolocalisation IP il faudrait ajouter une base GeoIP (optionnel).
Contenu : oui — recherche de mots-clés d'arnaque et détection de formulaires/faux inputs.
Comportement : partiellement — on capture téléchargements déclenchés (Playwright downloads), on interroge navigator.permissions pour détecter demandes de permissions (caméra/micro/copie/notifications), et on capture redirections et requêtes (heavy activity). Mesurer le CPU de la machine distante n'est pas possible à distance.
Réputation : oui (VirusTotal, urlscan) et optionnel Google Safe Browsing si tu fournis la clé.
Headers HTTP : oui — le script stocke et analyse les headers de la réponse statique (CSP, HSTS, X-Frame-Options, etc.) et retourne un objet security_headers.
Points additionnels et recommandations

Sécurité opérationnelle : exécute phish_core dans un container/sandbox (Docker) pour éviter risques (malicious JS, téléchargements). Limite aussi la mémoire/CPU et isole le réseau si nécessaire.
Quotas API : VirusTotal, urlscan, Google ont des quotas ; gère erreurs et backoffs.
Améliorations possibles (si tu veux que je les ajoute) :
GeoIP pour IPs retournées (pays, org) ; nécessite module geoip2/base.
Persistance des jobs dans une base (sqlite) — utile pour historique complet.
Intégration d'autres services (Google Safe Browsing déjà ajoutée optionnellement).
Analyse plus poussée de JS obfusqué (déobfuscation automatisée) — couteux mais faisable.
