[comment]: # (AUTOGENERATED MARKDOWN CONTENT. UPDATES TO ODM DOCUMENTATION SHOULD BE DONE THROUGH ASSEMBLYLINE-BASE REPO!)
# SubmissionSummary
> Submission Summary Model

| Field | Type | Description | Required | Default |
| :--- | :--- | :--- | :--- | :--- |
| classification | Classification | Classification of the cache | :material-checkbox-marked-outline: Yes | `TLP:W` |
| filtered | Boolean | Has this cache entry been filtered? | :material-checkbox-marked-outline: Yes | `False` |
| expiry_ts | Date | Expiry timestamp | :material-checkbox-marked-outline: Yes | `None` |
| tags | Text | Tags cache | :material-checkbox-marked-outline: Yes | `None` |
| attack_matrix | Text | ATT&CK Matrix cache | :material-checkbox-marked-outline: Yes | `None` |
| heuristics | Text | Heuristics cache | :material-checkbox-marked-outline: Yes | `None` |
| heuristic_sections | Text | All sections mapping to the heuristics | :material-checkbox-marked-outline: Yes | `None` |
| heuristic_name_map | Text | Map of heuristic names to IDs | :material-checkbox-marked-outline: Yes | `None` |

