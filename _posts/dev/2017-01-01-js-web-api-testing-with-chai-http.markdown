---
layout: post
title: 'JS: Web Server Testing with "chai-http"'
date: 2017-01-01 06:13:11.000000000 +07:00
tags: js
---
We're going to test a web api using `GET`, `POST` using `chai-http` by actual http request.

`chai` is not a test runner, it's just a javascript testing framework (aka. asserter). If you are looking for a test runner go see `mocha`

Note: You should install `mocha` globally (npm install -g mocha) and install `chai` locally.

To get started, install `chai` and `chai-http`:

```
npm install --save-dev chai chai-http
```

You should have your destination web server in place because I will show only the test part, here it is:

```
const chai = require('chai')
const chaiHttp = require('chai-http')
chai.use(chaiHttp)

describe('web server test', () => {
    it('test post request with file upload', (done) => {
        chai.request('http://....:...')
            .post('/api/post/test')
            .attach('image_field', img, 'upload.png')
            .end((err, res) => {
                if (err) {
                    console.error(err)
                } else {
                    const body = res.text
                    chai.expect(body).to.be.equal(....)
                    done()
                }
            })
    })
})
```

To not underestimate my reader's ability, I feel like there is no need to explain it in details. There is so much more to discover, yet beginning from reading `body` response from the request, I think, is a good start !
