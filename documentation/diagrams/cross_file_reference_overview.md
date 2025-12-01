flowchart LR

    resource[resource.xml]
    stops[stop.xml]
    linefile[line_LineId.xml]

    %% References from line files
    linefile -->|"StopPlaceRef / QuayRef"| stops
    linefile -->|"OperatorRef / DayTypeRef"| resource

    %% Codespaces
    resource -. "codespaces used in all IDs" .- stops
    resource -. "codespaces used in all IDs" .- linefile
