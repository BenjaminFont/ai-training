# SKILL Aufgabe

Erstelle einen Review-Skill für Merge Requests

Euer Team reviewt regelmäßig Merge Requests. Dabei prüft ihr immer wieder die gleichen Dinge — Testabdeckung, Coding-Konventionen, Fehlerbehandlung. Dieser  
Prozess lässt sich als Skill für Claude Code abbilden.

Eure Aufgabe:                                                                                                                                                
Erstellt einen Skill /review-mr, der den aktuellen Branch gegen den Hauptbranch vergleicht und ein Review nach euren Team-Standards ausgibt.

## Skeleton

---                                                                                                                                                          
name: review-mr                                                                                                                                              
description: Reviewt einen Merge Request nach unseren Team-Standards                                                                                         
user-invocable: true                                                                                                                                         
allowed-tools: Bash(git *), Grep, Glob, Read
---                                                                                                                                                          

# Merge Request Review

## Ziel
Analysiere einen Merge Request und gib ein strukturiertes Review                                                                                             
nach unseren Team-Standards aus.

## Schritt 1: MR-Änderungen laden

Nutze Git um die Änderungen des aktuellen Branches gegenüber dem                                                                                             
Hauptbranch zu ermitteln:
- `git diff main...HEAD` — zeigt alle Änderungen
- `git log main...HEAD --oneline` — zeigt die Commits
- `git diff main...HEAD --name-only` — zeigt geänderte Dateien

## Schritt 2: Team-Standards anwenden

TODO: Nach welchen Kriterien soll Claude den Code reviewen?

## Schritt 3: Review ausgeben

TODO: Wie soll das Ergebnis aussehen?                                                                                                                        
                                       