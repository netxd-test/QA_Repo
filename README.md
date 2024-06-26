# QA_Repo





name: Test
description: 'composite run action'

runs:
  using: "composite"
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          fetch-depth: '0'
          token: ${{ secrets.BOT_TOKEN }}

      - name: Generate Git Tag
        id: generate_tag
        run: |
          # Find the latest tag if available
          LATEST_TAG=$(git describe --tags --abbrev=0 || echo "")
          echo "Old tag: $LATEST_TAG"

          #Find Branch_Name

          Branch_Name="qa"
          echo "Branch Name:$Branch_Name"

          if [ -z "$LATEST_TAG" ]; then
            # If no tags found, start with initial version and branch name
            NEW_TAG="v0.0.0_$Branch_Name"
          else
            # Extract the version number and branch name from the latest tag
            VERSION_BRANCH=$(echo "$LATEST_TAG" | cut -d'_' -f1)
            VERSION=$(echo "$VERSION_BRANCH" | grep -oE '[0-9]+\.[0-9]+\.[0-9]+')
            BRANCH_NAME=$(echo "$LATEST_TAG" | cut -d'_' -f2)

            # Increment the version number
            NEW_VERSION=$(echo "$VERSION" | awk -F'.' '{print $1 "." $2 "." $3 + 1}')

            # Construct the new tag
            NEW_TAG="v${NEW_VERSION}_$Branch_Name"
          fi

          echo "Generated new tag: $NEW_TAG"
          echo "NEW_TAG=$NEW_TAG" >> $GITHUB_ENV

      - name: Push Git Tag
        env:
          BOT_TOKEN: ${{ secrets.BOT_TOKEN }}
        
        run: |
  
          git config user.name "Abisheck26"
          git config user.email "abisheckgnanavel@gmail.com"
          git tag $NEW_TAG          
          git push origin --tags >/dev/null 2>&1


