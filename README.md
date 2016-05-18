# tpa-globals

This repository has two distinct elements `tpa-globals` and `tpa-globals-query`. The internals of these elements relies on `iron-meta`. The purpose of `tpa-globals` is to address a couple of problems:

* Global static data sharing such as configuration, default language setting, and URL for service calls
* Global client-side updatable data sharing that can be used throughout the Single Page App (SPA) lifecycle which can be used to update values in consuming elements via data binding `{{}}`

## tpa-globals

`tpa-globals` element is utilized to define upfront the data that will be shared throughout the SPA lifecycle regardless of whether they are static or updatable data. NOTE that this element should only be used once throughout the SPA and is has to be the first element placed in the main html page before other custom elements.

IMPORTANT: Ensure `id` and `key` are the same values when defining `iron-meta`

To define the data inside the index.html using tpa-globals element:

```
<head>
	<!-- Other head declaration -->
	<link rel="import" href="bower_components/tpa-globals/tpa-globals.html">
</head>

<body>
	<tpa-globals>
		<iron-meta id="name" key="name" value="john"></iron-meta>
		<iron-meta id="token" key="token"></iron-meta> <!-- string-->
		<iron-meta id="services" key="services" value='{"login": "/services/login"}'></iron-meta> <!-- JS Object -->
		<iron-meta id="accountSummaryData" key="accountSummaryData"></iron-meta> <!-- Value can also be set via javascript -->
		<!-- Define additional keys using iron-meta -->
	</tpa-globals>
	<!-- Other elements -->
</body>


For `static` data such as configuration, you can then use the set-up values, for example the services object utilizing `iron-meta-query` element

```
<link rel="import" href="../polymer/iron-meta/iron-meta.html">

<iron-meta-query id="query" key="services" value="{{services}}"></iron-meta-query>
<iron-ajax url="{{services.login}}" ...></iron-ajax>
```

NOTE: using `iron-meta-query` will only get the data once off hence should be used for static data that never changed. For the dynamic data binding for the values that are set in `tpa-globals` will be discussed later in `tpa-globals-query` element. 


`tpa-globals` element also expose `setValue(key,val)` function in order to update some values of a key. It is highly recommended that when setting/updating data, particularly the dynamic ones we always update the whole Object not the child objects. E.g. instead of `client.token` we just use a key called `token`. The function `setValue` will update the `value` according to the `key` and will propagate the values to the `tpa-globals-query` element.

## tpa-globals-query

`tpa-globals-query` element will enable us to use data binding for shared data when writing our components.

Example:

```
<tpa-globals-query id="globals" key="name" value="{{name}}"></tpa-globals-query>
<div>Name :{{name}}</div>
```

When updates to the value of a particular key, this element will get "notified"

This example of the code is an example of updating the shared data globally:

```
document.querySelector('tpa-globals').setValue('name','smith')
```