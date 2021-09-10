# Classification engine

Assemblyline has the ability to do record-based access control for submission, files, results and even up to individual section and tags of said results. It was built to support the [Governement of Canada levels of security](https://www.tpsgc-pwgsc.gc.ca/esc-src/protection-safeguarding/niveaux-levels-eng.html) and the [Traffic Light Protocol](https://www.cisa.gov/tlp) but can also be modified at will. 

Once turned on, the classification engine will do the following changes to the system:

* User will have to be assigned a maximum classification level that they can see as well as the groups they are members of
* Each submissions to the system will have to have a classification level
* The User Interface will:
    * Show the effective classification of each submissions, files, results and result sections
    * Have a dedicated help section that will explain how classification conflicts are resolved
    * Will let you pick a classification while submitting a file
    * Will automatically hide portions of result for a user that does not have enough priviledges to see them

# Configuration 

The classification engine has many parameters that can be customized so you can get record-based access controls that fits your organization.

Here is an exhaustive configuration file of the classification engine that explain every single parameters:
???+ example "Exhaustive classification configuration"
    ```yaml
    # Turn on/off classification enforcement. When this flag is off, this
    #  completely disables the classification engine, any documents added while
    #  the classification engine is off gets the default unrestricted value
    enforce: false

    # When this falg in on, the classification engine will automatically create 
    #  groups based of the domain part of a user's email address
    #  EX: 
    #     For to user with email: test@local.host 
    #     The group "local.host" will be valid in the system 
    dynamic_groups: false

    # List of Classification level:
    #   This is a graded list, a smaller number is less restricted then an higher number
    #   A user must be allowed a classification level >= to be able to view the data
    levels:
      # List of alternate names for the current marking. If a user submits a file with 
      #  those markings, the classification will automatically rename it to the value 
      #  specified in name
      - aliases:
          - UNRESTRICTED
          - UNCLASSIFIED
          - U
        
        # Stylesheet applied in the UI for the current classification level
        css:
          # Name of the color scheme used for display 
          # Possible values: default, primary, secondary, success, info, warning, error
          color: default

          # Deprecated parameters (Use color instead)
          #  This where used in the old UI but are still valid in the new UI because if 
          #  the new UI cannot find the color param, it will use the label param and 
          #  strips "label-" part.
          banner: alert-default
          label: label-default
          text: text-muted
        
        # Description of the classification level
        description: 
          Subject to standard copyright rules, TLP:WHITE information may be distributed 
          without restriction.
        
        # Interger value of the Classification level (higher is more classified)
        lvl: 100
        
        # Long name of the classification level
        name: TLP:WHITE
        
        # Short name of the classification level
        short_name: TLP:W
      - aliases: []
        css:
          color: success
        description:
          Recipients may share TLP:GREEN information with peers and partner organizations
          within their sector or community, but not via publicly accessible channels. 
          Information in this category can be circulated widely within a particular 
          community. TLP:GREEN information may not be released outside of the community.
        lvl: 110
        name: TLP:GREEN
        short_name: TLP:G
      - aliases:
          - RESTRICTED
        css:
          color: warning
        description:
          Recipients may only share TLP:AMBER information with members of their
          own organization and with clients or customers who need to know the information
          to protect themselves or prevent further harm.
        lvl: 120
        name: TLP:AMBER
        short_name: TLP:A

    # List of required tokens:
    #   A user requesting access to an item must have all the
    #   required tokens the item has to gain access to it
    required:
      # List of alternate names for the token
      - aliases: []
        
        # Description of the token
        description: Produced using a commercial tool with limited distribution
        
        # Long name for the token
        name: COMMERCIAL

        # Short name for the token
        short_name: CMR
        
        # (optional) The minimum classification level an item must have
        #  for this token to be valid. So because this token has a value 
        #  of 120, once its selected, the classification level automatically 
        #  jumps to TPL:A which was set to 120 in the previous section. 
        require_lvl: 120

    # List of groups:
    #   A user requesting access to an item must be part of a least
    #   of one the group the item is part of to gain access
    groups:
      # List of aliases for the group
      - aliases: 
          - ANY
        # (optional) This is a special flag that when set to true, if any other groups 
        #  are selected in a classification, this group will automatically be selected 
        #  as well. 
        auto_select: true

        # Description of the group
        description: Employees of CSE

        # Long name for the group
        name: CSE

        # Short name for the group
        short_name: CSE

        # (optional) Assuming that this groups is the only group selected, this is the
        #   display name that will be used in the classification 
        #   NOTE: that values has to be in the aliases of this group and only this group
        solitary_display_name: ANY

    # List of sub-groups:
    #   A user requesting access to an item must be part of a least
    #   of one the sub-group the item is part of to gain access
    subgroups:
      # List of aliases for the subgroups
      - aliases: []

        # Description of the sub-group
        description: Member of Incident Response team

        # Long name of the sub-group
        name: IR TEAM

        # Short name of the sub-group
        short_name: IR
      - aliases: []
        description: Member of the Canadian Centre for Cyber Security
        
        # (optional) This is a special flag that auto-select the corresponding
        #   group when this sub-group is selected 
        require_group: CSE
        
        name: CCCS
        short_name: CCCS
        
        # (optional) This is a special flag that makes sure that none other then
        #   the corresponding group is selected when this sub-group is selected 
        limited_to_group: CSE

    # Default restricted classification
    restricted: TLP:A//CMR

    # Default unrestricted classification:
    #   When no classification are provided or that the classification engine is
    #   disabled, this is the classification value each items will get
    unrestricted: TLP:W
    ```

# Enabling it in your system
By default, the classification engine is disabled in the system but it can easily be enabled by creating a new config map in kubernetes. 

!!! info 
    For the purpose of this documentation we will enable a classification engine which support only the traffic light protocol.

In your deployment directory, create a file named `objects.yaml` with the following content:
???+ example "objects.yaml"
    ```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: assemblyline-extra-config
    data:
      classification: |
        enforce: true
        levels:
          - aliases:
              - UNRESTRICTED
              - U
            css:
              color: default
            description: 
              Subject to standard copyright rules, TLP:WHITE information may be 
              distributed without restriction.
            lvl: 100
            name: TLP:WHITE
            short_name: TLP:W
          - aliases: []
            css:
              color: success
            description:
              Recipients may share TLP:GREEN information with peers and partner 
              organizations within their sector or community, but not via publicly 
              accessible channels. Information in this category can be circulated 
              widely within a particular community. TLP:GREEN information may not 
              be released outside of the community.
            lvl: 110
            name: TLP:GREEN
            short_name: TLP:G
          - aliases:
              - RESTRICTED
              - R 
            css:
              color: warning
            description:
              Recipients may only share TLP:AMBER information with members of their
              own organization and with clients or customers who need to know the 
              information to protect themselves or prevent further harm.
            lvl: 120
            name: TLP:AMBER
            short_name: TLP:A

        required: []
        groups: []
        subgroups: []
        
        restricted: TLP:A
        unrestricted: TLP:W
    ```

Use kubectl to apply the `objects.yaml` file to your system:

=== "Appliance"

    ```shell
    sudo microk8s kubectl apply -f ~/git/deployment/objects.yaml --namespace=al
    ```

=== "Cluster"

    ```shell
    kubectl apply -f <deployment_directory>/objects.yaml --namespace=al
    ```