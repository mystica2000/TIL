# XSS Sanitizer

To prevent xss injection, ALWAYS Sanitize the user input before adding it to the DOM;


1. Sanitize the user input (html string) using any sanitizer ([DOMPurify](https://www.npmjs.com/package/dompurify))

2. display in template: 

  - In Angular, 
     since the template has prevention againt xss, use ```DomSanitizerObj.bypasssecuritytrusthtml(sanitized_string)``` to display it on ```<div [innerHTML]="htmlStr"></div>```