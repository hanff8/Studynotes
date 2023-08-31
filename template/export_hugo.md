<%*
const CONFIG = {
  STYLE: {
    FILENAME: "title" // "title" | "timestamp" | override by permanent_link
  },
  HUGO_FOLDERNAME: "quartz" //修改为你的hugo子模块目录名
};
/* FileName */
let FILENAME;
if (tp.frontmatter["permanent_link"]) {
  FILENAME = tp.frontmatter["permanent_link"];
} else {
  if (CONFIG.STYLE.FILENAME === "timestamp") {
    FILENAME = tp.date.now("YYYYMMDDHHmmss");
  } else {
    FILENAME = tp.file.title;
  }
};
const FOLDER_PATH = `${CONFIG.HUGO_FOLDERNAME}/content/notes`;
const FILE_PATH = `${FOLDER_PATH}/${FILENAME}.md`;
const RAW = tp.file.content;
const FRONTMATTER = `---
title: "${tp.file.title}"
date: "${tp.file.creation_date("YYYY-MM-DD")}"
description: ""
tags: [ ${tp.file.tags.join(", ").replaceAll("#", "")} ]
---
`;
const CONTENT = tp.file.content.replace(/---\n[\s\S]*?---\n/gm, "");
if (tp.file.exists(FILE_PATH)) {
  await app.vault.delete(tp.file.find_tfile(FILE_PATH));
}
await tp.file.create_new(
  FRONTMATTER + CONTENT,
  FILENAME,
  true,
  app.vault.getAbstractFileByPath(FOLDER_PATH)
);

%>