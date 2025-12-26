
# FaithInterFolia API Documentation

## Overview

This API provides access to the Knowledge Graph data for the FaithInterFolia project. It allows frontend applications to search for entities (Works, Persons, Events, etc.), retrieve detailed information cards, generate network graphs, and fetch associated images.

**Base URL:**  https://interfoliafructus.unimi.it (api on: http://swarmstg-master2.srv.ict.unimi.it:9001)
#### Common Headers

-   Content-Type: application/json
    

#### Response Format

All successful responses return JSON. Errors return a standard JSON error object:

codeJSON

```
{
  "detail": "Error message description"
}
```

----------

## 1. System Status

### Check Readiness

Checks if the backend has finished loading all data from the database into the cache. The frontend should check this before allowing searches.

-   **Endpoint:**  GET /health/ready
    
-   **Success Response (200 OK):**
    
    codeJSON
    
    ```
    {
      "status": "ready"
    }
    ```
    
-   **Loading Response (503 Service Unavailable):**
    
    codeJSON
    
    ```
    {
      "status": "loading_data"
    }
    ```
    

----------

## 2. Metadata & Filters

### Get Filter Options

Retrieves all available options to populate frontend dropdowns (e.g., list of classifications, projects).

-   **Endpoint:**  GET /filters/options
    
-   **Description:** Returns a dictionary where keys are project names (or __ALL__) and values are lists of available strings for each field.
    
-   **Example Response:**
    
    codeJSON
    
    ```
    {
      "__ALL__": {
        "projects": [
          "Postillati ambrosiani"
        ],
        "classifications": [
          "Archivi Di Dati",
          "Astronomia",
          "Bibliografia",
          "Biblioteche Private",
          "Biografie Greche",
          "Carta",
          "Cataloghi Bibliografici",
          "Commedia Greca"
        ]
      }
    }
    ```
    

----------

## 3. Entity Search

### Search Entities

The main endpoint for retrieving lists of items (cards).

-   **Endpoint:**  POST /entities/search
    
-   **Description:** Performs a complex filtered search.
    
-   **Request Body Example:** Find works titled "[Plutarchi] De Homero" OR "Ad Demonicum" that also have the classification "Archivi Di Dati".
    
    codeJSON
    
    ```
    {
      "projects": [
        "Postillati ambrosiani"
      ],
      "entity": "work",
      "rules": [
        {
          "field": "work_title",
          "logic": "and",
          "values": [
            "[Plutarchi] De Homero",
            "Ad Demonicum"
          ],
          "op": "equals"
        },
        {
          "field": "classification",
          "logic": "and",
          "values": [
            "Archivi Di Dati"
          ],
          "op": "equals"
        }
      ]
    }
    ```---
    ```
    

## 4. Entity Details

### Get Single Entity

Retrieves the full detailed "card" for a specific item, including its relationships.

-   **Endpoint:**  GET /details/{entity_type}/{entity_id}
    
-   **Parameters:**
    
    -   entity_type: One of the supported types (e.g., work, person).
        
    -   entity_id: The unique database ID string.
        
-   **Example Request:**  GET /details/work/694bbf7a24d1fd1b1de01e04
    
-   **Example Response:**
    
    codeJSON
    
    ```
    {
      "work_title": "Ad Demonicum",
      "relationships": [
        {
          "type": "visual_object_is_mentioning",
          "direction": "incoming",
          "group": "mention",
          "source_id": "694bbf7624d1fd1b1de01468",
          "source_type": "visual_object",
          "source_label": "VO_PAG_AMBR_INC_1132_Α6r_23",
          "source_card": { "hypotheses": [] }
        },
        {
          "type": "work_has_classification",
          "direction": "outgoing",
          "group": "other",
          "target_id": "694bbf7b24d1fd1b1de01f06",
          "target_type": "work",
          "target_label": "Oratoria greca",
          "target_card": { "hypotheses": [] }
        },
        {
          "type": "work_authored_by",
          "direction": "outgoing",
          "group": "other",
          "target_id": "694bbf7724d1fd1b1de01736",
          "target_type": "person",
          "target_label": "Isocrates Atheniensis",
          "target_card": {
            "hypotheses": [],
            "name": "Isocrates Atheniensis",
            "birth_date": "435",
            "death_date": "338",
            "projects": ["Postillati ambrosiani"]
          }
        }
      ],
      "projects": ["Postillati ambrosiani"]
    }
    ```
    

----------

## 5. Graphs

### Generate Graph Data

Generates nodes and edges for data visualization.

-   **Endpoint:**  POST /graphs/search
    
-   **Response Format (Example):**
    
    codeJSON
    
    ```
    {
      "nodes": [
        {
          "id": "694bbf7624d1fd1b1de014a1",
          "label": "ex_in_aristotelis_analyticorum_priorum_alexander_aphrodisiensis_grc_text_1",
          "title": "In Aristotelis Analyticorum priorum - Testo originario - 1",
          "entity_type": "expression",
          "projects": ["Postillati ambrosiani"]
        },
        {
          "id": "694bbf7a24d1fd1b1de01e0c",
          "label": "w_in_aristotelis_analyticorum_priorum_alexander_aphrodisiensis",
          "title": "In Aristotelis Analyticorum priorum",
          "entity_type": "work",
          "projects": ["Postillati ambrosiani"]
        }
      ],
      "edges": []
    }
    ```
    

----------

## 6. Images

### Serve Image

Retrieves a static image file associated with a project.

-   **Endpoint:**  GET /images/{project_name}/{image_name}
    
-   **Description:** This endpoint automatically detects the file extension (.jpg, .png) so you don't need to know it.
    
-   **Example Request:**  https://interfoliafructus.unimi.it/images/Biblioteca%20Claudel/BJd50098
    
    -   **Note:** Do not include .jpg in the request. The API will find BJd50098.jpg or BJd50098.png automatically.
        

----------

## Appendix: Reference Lists

### Supported Entity Types (entity)

Use these strings for the entity parameter in search and details endpoints.

-   work
    
-   expression
    
-   manifestation
    
-   item
    
-   page
    
-   person
    
-   visual_object
    
-   physical_object
    
-   institution
    
-   event
    
-   abstract_character
    

### Operators (op)

-   equals (Exact match)
    
-   contains (Substring match)
    
-   phrase (Exact sequence of words)
    
-   any_word (Matches if any of the words are present)
    
-   all_words (Matches only if all of the words are present)
    

### Full List of Filter Fields

These are the valid values for the field key in your search payload.

#### Common / General

-   projects: Filter by project name (e.g., "Biblioteca Claudel").
    
-   proximity_text_search: Advanced full-text search to find words near each other (requires proximity_query).
    

#### Work & Expression Filters

-   work_title: The title of the work.
    
-   author: The author of the work.
    
-   classification: The genre or category of the work (e.g., "Astronomy", "Biography").
    
-   type_of_expression: The form of the text (e.g., "Translation", "Original Text").
    
-   language: The language of the expression (e.g., "Latin", "Greek").
    
-   role_of_person_or_institution: The responsibility statement for an expression (e.g., 'curante et imprimente').
    
-   translator: The person who translated the work.
    
-   expression_editor: The editor of the specific expression.
    
-   scriptwriter: The scriptwriter (if applicable).
    
-   compositor: The compositor (if applicable).
    
-   reviewer: The reviewer of the text.
    
-   other_secondary_role: Any other secondary contributor role not explicitly listed.
    
-   search_for_roles_in_expression: A "meta-filter" that searches across all expression roles (Translator, Editor, etc.) at once.
    

#### Manifestation (Publication) Filters

-   publisher: The publisher of the book/manifestation.
    
-   publication_date: The year or date range of publication.
    
-   place: The city or location of publication.
    
-   editor: The editor of the publication.
    
-   corrector: The corrector of the publication.
    
-   sponsor: The sponsor of the publication.
    
-   search_for_roles_in_manifestation: A "meta-filter" that searches across all manifestation roles at once.
    

#### Item (Physical Copy) Filters

-   type_of_item: The physical format (e.g., "Volume", "Manuscript").
    
-   material: The material used (e.g., "Paper", "Parchment").
    
-   preservation_status: The condition of the item.
    
-   owner: The current or past owner of the item.
    
-   digitalization: Information regarding the digitalization status.
    

#### Person Filters

-   person_name: The full name of the person.
    
-   person_role: The general role of the person in the database.
    
-   person_gender: Gender of the person.
    
-   person_birth_date: Year of birth.
    
-   person_death_date: Year of death.
    
-   person_or_institution: Matches entities that are either a person OR an institution.
    

#### Visual Object Filters (Images/Annotations)

-   type_of_visual_object: Category of the visual element (e.g., "Marginalia", "Illustration").
    
-   visual_object_transcription: Search within the transcribed text of the annotation.
    
-   visual_object_function: The purpose of the annotation (e.g., "Ownership", "Commentary").
    
-   visual_object_instrument: The tool used (e.g., "Pen", "Pencil").
    
-   visual_object_colour: The color of the ink or material.
    
-   visual_object_language: The language of the annotation.
    
-   visual_object_owner: The owner indicated in the visual object.
    
-   visual_object_inscriber: The person who wrote the annotation.
    
-   visual_object_sender: The sender (for letters/notes).
    
-   visual_object_recipient: The recipient (for letters/notes).
    
-   roles_related_to_visual_object: Search across all roles (Owner, Inscriber, etc.) linked to the object.
    

#### Physical Object Filters

-   type_of_physical_object: Category (e.g., "Loose Sheet", "Card").
    
-   physical_object_place: Place associated with the object.
    
-   physical_object_date: Date associated with the object.
    
-   roles_related_to_physical_object: Search across roles linked to the physical object.
    

#### Institution & Event Filters

-   institution_name: Name of the institution (e.g., "Biblioteca Ambrosiana").
    
-   institution_place: Location of the institution.
    
-   institution_role: The role of the institution in the database (e.g., 'Publisher', 'Owner of item').
    
-   event_name: Name of the historical event.
    
-   event_date: Date the event occurred.
    

#### Abstract Character Filters

-   abstract_character_name: Name of a fictional or abstract character.
    
-   abstract_character_mentioned_in: Filters characters based on where they appear (e.g., "Mentioned in a Work").
