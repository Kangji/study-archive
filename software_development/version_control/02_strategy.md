# Branching Strategy

## Git Flow

- **master**
    - stable & always on production-ready state
    - tag with release version
- **develop**
    - contains development changes for next release
- **feature**
    - implement new feature
    - merge into develop
- **release**
    - detailed check for release
    - merge into develop & master
- **hotfix / patch**
    - fix urgent bugs from **master**
    - merge into develop & master

## GitHub Flow

- Better for short release-interval
- **master is always deployable**
- **feature branch - pr - review - testing - deploy - merge**
