```js
import axios from "axios";

function useAxios() {
  const instance = axios.create({
    timeout: 1000,
    // baseURL: "http://localhost:3000"
    headers: {
      "Content-Type": "application/json",
      Authorization: "Bearer SOMEJWTTOKEN",
    },
  });
  
  instance.interceptors.request.use(
    (config) => {
      console.log(config);
      return config;
    },
    (error) => {
      return Promise.reject(error);
    }
  );

  instance.interceptors.response.use(
    (response) => {
      console.log(response);
      return response;
    },
    (error) => {
      console.log("发生了错误", error);
      return Promise.reject(error);
    }
  );

  return instance;
}

export default useAxios;
```
