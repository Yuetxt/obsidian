----
creation date: <%+ tp.file.creation_date("YYYY-MM-DD hh:mm") %>
tags: <%+tp.file.title.split('-')[1]%>-<%+tp.file.title.split('-')[2]%>
---

modification date: <%+ tp.file.last_modified_date("dddd Do MMMM YYYY hh:mm:ss") %>

# <%+ tp.file.title %>

<< [[<%+ tp.date.now("YYYY-MM-DD", -1)%>]] | [[<%+ tp.date.now("YYYY-MM-DD", 1) %>]] >>

## Tasks

#### Over Due

```tasks
not done
due before <% tp.date.now("YYYY-MM-DD") %>
```

#### Due Today

```tasks
not done
due on <% tp.date.now("YYYY-MM-DD") %>
```

#### New Today
- [ ]

## Meeting Log

### 0000:

## Daily Log

### [[Project 1]]


### [[Project 2]]


### [[Project 3]]

## Daily Check List

### Start of Day

- [ ] Check Email

### End of Day

- [ ] Leetcode Problem

## Other Tasks

#### No Due Date

```tasks
not done
no due date
```

#### Done Today

```tasks
done on <% tp.date.now("YYYY-MM-DD") %>
```