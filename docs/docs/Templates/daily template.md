---
created: <% tp.file.creation_date() %>
---
tags:: [[+Daily Notes]]

# <% moment(tp.file.title,'dddd DD MMMM YYYY').format("dddd DD MMMM YYYY") %>

<< [[<% fileDate = moment(tp.file.title, 'dddd DD MMMM YYYY').subtract(1, 'd').format('dddd DD MMMM YYYY') %>|Yesterday]] | [[<% fileDate = moment(tp.file.title, 'dddd DD MMMM YYYY').add(1, 'd').format('Ydddd DD MMMM YYYY') %>|Tomorrow]] >>

```tasks

#   '- [ ] ' or
#   '* [ ] ' or
#   '1. [ ] '


# Tasks due today or earlier:
due before tomorrow


limit 100


group by filename
sort by due reverse
sort by description

```


---
# 📝 Notes
- <% tp.file.cursor() %>

---
### Notes created today
```dataview
List FROM "" WHERE file.cday = date("<%tp.date.now("YYYY-MM-DD")%>") SORT file.ctime asc
```

### Notes last touched today
```dataview
List FROM "" WHERE file.mday = date("<%tp.date.now("YYYY-MM-DD")%>") SORT file.mtime asc
```
