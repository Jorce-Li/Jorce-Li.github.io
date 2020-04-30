---
layout: post
title: "flexsearch使用+worker"
subtitle: '全局搜索'
author: "Jorce"
header-style: text
tags:
  - flexsearch
  - worker
---

Update: 用户不愿意付费使用algolia，换位flexsearch

---

## algolia flexsearch

公司后端选择的全站搜索引擎是algolia，它支持react并有封装好的一系列react组件，后来用户不愿意付费所以进行更换

```js
/* eslint-disable no-await-in-loop */
/* eslint-disable no-restricted-globals */
let documents = null;

const httpGet = (url) => {
  return new Promise((resolve) => {
    const xhr = new XMLHttpRequest();
    xhr.addEventListener('load', () => {
      try {
        const responseData = JSON.parse(xhr.responseText);
        resolve(responseData);
      } catch (e) {
        resolve(xhr.responseText);
      }
    });
    xhr.open('GET', url);
    xhr.send();
  });
};

const getDocuments = async (baseURL) => {
  const url = baseURL + 'baseUrl'
  const response = await httpGet(url);
  response.forEach(({ items }) => {
    if (documents === null) {
      documents = [];
    }
    documents = documents.concat(items);
  });
};

const initSearch = async ({ baseURL }) => {
  if (documents === null) {
    await getDocuments(baseURL);
    // buildIndex();
    postMessage({ action: 'initSearch' });
  }
};

const dumbSearch = (query) => {
  const safeQuery = query.replace(/[.*+?^${}()|[\]\\]/gi, '\\$&');
  const queryParts = safeQuery
    .split(' ')
    .map(p => p.replace(/^(the|of|de|&|by|and|an?|with|this|that|these|or|from|as|at|for)$/gi, ''))
    .filter(p => p.length > 0);
  if (queryParts.indexOf(safeQuery) === -1) {
    queryParts.unshift(safeQuery);
  }
  const regexps = queryParts.map(q => new RegExp(q, 'gi'));
  const fields = [
    ['title', { boost: 10, tokenize: true }],
    ['body', { boost: 0.5 }],
    ['summary'],
    ['cuisine_types', { boost: 5 }],
    ['category', { boost: 5 }],
    ['type', { boost: 8 }],
    ['hotel'],
    ['subtitles', { boost: 2 }],
    ['meta'],
    ['category'],
    ['rooms'],
    ['path', { boost: 3 }],
  ];

  const ids = [];
  const results = documents
    .map(doc => {
      let score = 0;
      fields.forEach(([fieldname, options = {}]) => {
        const { boost = 1, tokenize = false } = options;
        let value = doc[fieldname];
        if (value === undefined || value === null) {
          return;
        }
        if (Array.isArray(value)) {
          value = value.join(' ');
        }
        regexps.forEach((regexp, i) => {
          const matches = value.match(regexp);
          if (matches === null || matches.length === 0) {
            return;
          }
          const exact = i === 0 ? 20 : 0.5;
          let wordMatch = !exact ? 0.3 : 1;
          if (tokenize && !exact) {
            const tokens = value.replace(/[,.?;:"“-]/gi, '').toLowerCase().split(' ');
            const match = matches[0].toLowerCase();
            if (tokens.indexOf(match) >= 0) {
              wordMatch = 1;
            }
          }
          score += (1 + Math.log(matches.length)) * boost * exact * wordMatch;
        });
      });
      return [score, doc];
    })
    .filter(([score]) => score > 1)
    .sort((res1, res2) => res1[0] > res2[0] ? -1 : 1)
    .reduce((out, [, doc]) => {
      if (ids.indexOf(doc.uuid) >= 0) {
        return out;
      }
      ids.push(doc.uuid);
      return [
        ...out,
        doc,
      ];
    }, []);
  return results;
};
const sleep = (duration) => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve();
    }, duration);
  });
};

const search = async ({ query }) => {
  while (documents === null) {
    await sleep(200);
  }
  postMessage({ action: 'search', results: dumbSearch(query) });
};

if (process.env.IS_BROWSER) {
  addEventListener('message', event => {
    const { data } = event;
    const { action, args } = data;

    switch (action) {
      case 'initSearch':
        initSearch(...args);
        break;

      case 'search':
        search(...args);
        break;

      default:
        break;
    }
  });
}

```