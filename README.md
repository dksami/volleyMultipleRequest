# Mulitple Volley Request at the Same Time
[![N|Solid](http://dashboards.tk/sami.png)](https://super-sami.com/)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

volleyMultipleRequest


# 3 Steps
* Import the interface
* Import the Class .
* Recivied Data .


### STEP 1
Add a Interface

```sh

import org.json.JSONObject;

public interface VolleyJsonRespondsListener {

    public void onSuccessJson(JSONObject result, String type);
    public void onFailureJson(int responseCode, String responseMessage);
}
```

### STEP 2
Add a Class

```sh

public class PostVolleyJsonRequest {
    private String  type;
    private Activity act;
    private VolleyJsonRespondsListener volleyJsonRespondsListener;
    private String networkurl;
    private JSONObject jsonObject = null;
    private JSONObject params;

    public PostVolleyJsonRequest(Activity act, VolleyJsonRespondsListener volleyJsonRespondsListener, String type, String netnetworkUrl,JSONObject params) {
        this.act = act;
        this.volleyJsonRespondsListener = volleyJsonRespondsListener;
        this.type = type;
        this.networkurl = netnetworkUrl;
        this.params = params;
        sendRequest();
    }

    private void sendRequest() {

        Log.d("url", "url" + networkurl);
        JsonObjectRequest jsObjRequest = new JsonObjectRequest(Request.Method.GET,networkurl,params,
                new Response.Listener<JSONObject>() {
                    @Override
                    public void onResponse(JSONObject response) {
                        Log.e("response", "response " + response);
                        volleyJsonRespondsListener.onSuccessJson(response, type);
                    }
                },
                new Response.ErrorListener() {
                    @Override
                    public void onErrorResponse(VolleyError error) {
                        try {
                            NetworkResponse response = error.networkResponse;
                            Log.e("response", "response " + response);
                            if (response != null) {
                                int code = response.statusCode;

                                String errorMsg = new String(response.data);
                                Log.e("response", "response" + errorMsg);
                                try {
                                    jsonObject = new JSONObject(errorMsg);
                                } catch (JSONException e) {
                                    e.printStackTrace();
                                }
                                String msg = jsonObject.optString("message");
                                volleyJsonRespondsListener.onFailureJson(code, msg);
                            } else {
                                String errorMsg = error.getMessage();
                                volleyJsonRespondsListener.onFailureJson(0, errorMsg);
                            }
                        } catch (Exception e) {
                            e.printStackTrace();
                        }
                    }
                });

        jsObjRequest.setRetryPolicy(new DefaultRetryPolicy(
                5000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));

        RequestQueue requestqueue = Volley.newRequestQueue(act);
        requestqueue.add(jsObjRequest);

    }}
```
### STEP 3
Import Interface
```sh
  @Override
    public void onSuccessJson(JSONObject result, String type) {
}
  @Override
    public void onFailureJson(int responseCode, String responseMessage) {

    }
```


### Usage 

Now for Sending a Request Just call the class in try catch like this 

```
            new PostVolleyJsonRequest(Mainfile.this, Mainfile.this, "TestData", url, params);
```

And fetch this data onSuccessJson like this
```  @Override
    public void onSuccessJson(JSONObject result, String type) {
   switch (type) {
            case "TestData":
              ##fetch data from here
                break;
       
   }
```

you can also latter modify the code and send what kind of request you are trying to send by just sending the request type as parameter .

**HAVE FUN**
