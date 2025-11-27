flowchart TD
    FILE["XML file"]

    FILE --> PD[PublicationDelivery]

    PD --> META["Metadata
• PublicationTimestamp
• ParticipantRef
• Description"]

    PD --> CF[CompositeFrame]

    subgraph FRAMES ["Business Frames"]
        RF[ResourceFrame]
        SF[ServiceFrame]
        SCF[ServiceCalendarFrame]
        SITEF[SiteFrame]
        TT[TimetableFrame]
    end

    CF --> FRAMES
