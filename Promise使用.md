## Promise



### abort promise
#### 实现fetchCanAbort
```ts
function fetchCanAbort(params, timeout = 30000): Promise<any> {
    const ABORT = 'ABORT';
    let abort = null;
    const abortPromise = new Promise((_resolve, reject) => {
        abort = () => {
            console.log(`fetch ${serviceCode} abort`);
            reject(ABORT);
        };
    });
    const fetchPromise = new Promise((resolve, reject) => {
        fetchData(params, timeout)
            .then(
            (response: any) => {
                resolve(response);
            },
            (error: any) => {
                reject(error);
            }
        );
    });
    const resPromise: any = Promise.race([
        fetchPromise,
        abortPromise
    ]);

    const abortRes: any = new Promise((resolve, reject) => {
        resPromise.then(
            (response: any) => {
                resolve(response);
            },
            (error: any) => {
                if (error !== ABORT) {
                    reject(error);
                }
            }
        );
    });
    abortRes.abort = abort;

    return abortRes;
}

```

#### API
```ts
 *  let api = fetchCanAbort('100000', {});
 *  if (isLoading) {
 *      api.abort();
 *  }
 *  api.then(res => console.log(res))
 * ```
