
[Source](http://docs.guzzlephp.org/en/stable/quickstart.html "Permalink to Quickstart — Guzzle Documentation")

# Bắt đầu nhanh - Tài liệu Guzzle

Đây là trang cung cấp một giới thiệu nhanh chóng về Guzzle và một vài ví dụ. Nếu bạn chưa cài đặt Guzzle hãy đi đến trang [Installation][1] 

## Tạo một Request

Bạn có thể gửi các request với Guzzle sử dụng một đối tượng `GuzzleHttpClientInterface`

### Tạo một Client
    
    
    use GuzzleHttpClient;
    
    $client = new Client([
        // URI cơ bản được sử dụng với các request quan hệ
        'base_uri' => 'http://httpbin.org',
        // Bạn có thể đặt bất kỳ các thuộc tính mặc định cho request
        'timeout'  => 2.0,
    ]);
    

Clients không thay đổi trong Guzzle 6 , Điều này có nghĩa là chúng ta không thể thay đổi giá trị mặc định được client thêm vào sau khi khởi tạo.

Khởi tạo client chấp nhận một tập hợp các mảng thuộc tính:

`base_uri`
: 

(string|UriInterface) URI cơ bản của client được kết hợp trong các URI tương đối. Có thể là một chuỗi hoặc một thể hiện của UriInterface.Khi một URI tương đối được khởi tạo từ một client,client sẽ biên dịch URI cơ bản với URI tương đối sử dụng các quy tắc được miêu tả trong [RFC 3986, section 2][2].
    
    
    // Khởi tạo một client với URI cơ bản
    $client = new GuzzleHttpClient(['base_uri' => 'https://foo.com/api/']);
    // Gửi một request tới https://foo.com/api/test
    $response = $client->request('GET', 'test');
    // Gửi một request tới https://foo.com/root
    $response = $client->request('GET', '/root');
    

Cảm thấy không mấy thích thú khi đọc RGC 3986? Đây là một vài ví dụ nhanh chóng về `base_uri` đã giải quyết đối với URI khác như thế nào.

| base_uri              | URI              | Result                   |  
| --------------------- | ---------------- | ------------------------ |  
| `http://foo.com`      | `/bar`           | `http://foo.com/bar`     |  
| `http://foo.com/foo`  | `/bar`           | `http://foo.com/bar`     |  
| `http://foo.com/foo`  | `bar`            | `http://foo.com/bar`     |  
| `http://foo.com/foo/` | `bar`            | `http://foo.com/foo/bar` |  
| `http://foo.com`      | `http://baz.com` | `http://baz.com`         |  
| `http://foo.com/?bar` | `bar`            | `http://foo.com/bar`     |  

`handler`
: (callable) Hàm để chuyển các request HTTP qua dây.Hàm này được gọi bởi một `Psr7HttpMessageRequestInterface` và mảng các thuộc tính chuyển đổi, và bắt buộc phải trả về một `GuzzleHttpPromisePromiseInterface`  được thi hành với một `Psr7HttpMessageResponseInterface` trả về khi thành công.`handler` là một hàm khởi tạo duy nhất không thể ghi đè trong các thuộc tính per/request

`...`
:(mixed) Tất các các thuộc tính khác thông qua hàm khởi tạo được sử dụng như các thuộc tính request mặc định với mọi request được tạo bởi client.

### Gửi các Request

Các phương thức ma thuật trên client được tạo ra thuận tiện cho việc gửi các request đồng bộ:
    
    
    $response = $client->get('http://httpbin.org/get');
    $response = $client->delete('http://httpbin.org/delete');
    $response = $client->head('http://httpbin.org/get');
    $response = $client->options('http://httpbin.org/get');
    $response = $client->patch('http://httpbin.org/patch');
    $response = $client->post('http://httpbin.org/post');
    $response = $client->put('http://httpbin.org/put');
    

Bạn có thể khởi tạo 1 request và sau đó gửi request đó với client khi bạn đã sẵn sàng:
    
    
    use GuzzleHttpPsr7Request;
    
    $request = new Request('PUT', 'http://httpbin.org/put');
    $response = $client->send($request, ['timeout' => 2]);
    

Các đối tượng phía client cung cấp một **deal** linh hoạt trong việc request được chuyển đổi bao gồm các thuộc tính request mặc định ra sao , bộ xử lý ngăn xếp mặc định được sử dụng bởi từng request , và một URI cơ bản cho phép bạn gửi các request với các URI tương đối.

Bạn có thẻ tìm thấy nhiều tài liệu vè middleware client trong tài liệu của trang [_Handlers and Middleware_][3].

### Async Requests
### Các request bất đồng bộ

Bạn có thể gửi các request bất đồng bộ khi sử dụng các phương thức ma thuật được cung cấp bởi một client:
    
    
    $promise = $client->getAsync('http://httpbin.org/get');
    $promise = $client->deleteAsync('http://httpbin.org/delete');
    $promise = $client->headAsync('http://httpbin.org/get');
    $promise = $client->optionsAsync('http://httpbin.org/get');
    $promise = $client->patchAsync('http://httpbin.org/patch');
    $promise = $client->postAsync('http://httpbin.org/post');
    $promise = $client->putAsync('http://httpbin.org/put');
    

Bạn cũng có thể sử dụng  2hàm sendAsync() và requestAsync()  của client:
    
    
    use GuzzleHttpPsr7Request;
    
    // Khởi tạo một đối tưởng PSR-7 để gửi request
    $headers = ['X-Foo' => 'Bar'];
    $body = 'Hello!';
    $request = new Request('HEAD', 'http://httpbin.org/head', $headers, $body);
    
    // Hoặc là , nếu bạn không cần phải thông qua trên một thực thể request
    $promise = $client->requestAsync('GET', 'http://httpbin.org/get');
    

promise được trả về bởi các phương thức trên bổ sung tại [Promises/A+ spec][4], được cung cấp bởi [Guzzle promises library][5].Điều này có nghĩa là bạn có thể xâu chuỗi `then()` để  gọi promise.sau đó các lệnh gọi được thực hiện với một  `PsrHttpMessageResponseInterface` thành công hoặc bị từ chối bởi một ngoại lệ.
    
    
    use PsrHttpMessageResponseInterface;
    use GuzzleHttpExceptionRequestException;
    
    $promise = $client->requestAsync('GET', 'http://httpbin.org/get');
    $promise->then(
        function (ResponseInterface $res) {
            echo $res->getStatusCode() . "n";
        },
        function (RequestException $e) {
            echo $e->getMessage() . "n";
            echo $e->getRequest()->getMethod();
        }
    );
    

### Các request đồng thời

Bạn có thể gửi nhiều request đồng thời bằng các promise và các request bất đồng bộ.
    
    
    use GuzzleHttpClient;
    use GuzzleHttpPromise;
    
    $client = new Client(['base_uri' => 'http://httpbin.org/']);
    
    // khởi tạo với mỗi request không chặn
    $promises = [
        'image' => $client->getAsync('/image'),
        'png'   => $client->getAsync('/image/png'),
        'jpeg'  => $client->getAsync('/image/jpeg'),
        'webp'  => $client->getAsync('/image/webp')
    ];
    
    // Chờ tất cả các request hoàn thành.Ném ra một ConnectException
    // nếu một vài request bị lỗi
    $results = Promiseunwrap($promises);
    
    // Đợi các request hoàn thành ngay cả khi một vài cái bị lỗi
    $results = Promisesettle($promises)->wait();
    
    // Bạn có thể chấp thuận mỗi kết quả trả về bằng cách dùng khóa được cung cấp để unwrap
    // hàm.
    echo $results['image']['value']->getHeader('Content-Length')[0]
    echo $results['png']['value']->getHeader('Content-Length')[0]
    

Bạn có thể sử dụng đối tượng `GuzzleHttpPool` khi bạn có một số request không xác định mà bạn muốn gửi
    
    
    use GuzzleHttpPool;
    use GuzzleHttpClient;
    use GuzzleHttpPsr7Request;
    
    $client = new Client();
    
    $requests = function ($total) {
        $uri = 'http://127.0.0.1:8126/guzzle-server/perf';
        for ($i = 0; $i < $total; $i++) {
            yield new Request('GET', $uri);
        }
    };
    
    $pool = new Pool($client, $requests(100), [
        'concurrency' => 5,
        'fulfilled' => function ($response, $index) {
            // điều này được gửi đến từng response thành công
        },
        'rejected' => function ($reason, $index) {
            // điều này được gửi đến từng response thất bại
        },
    ]);
    
    // Khởi tạo các chuyển đổi và tạo một promise
    $promise = $pool->promise();
    
    // Bắt buộc nhóm request hoàn thành
    $promise->wait();
    

Hoặc sử dụng closure sẽ trả về một promise khi cái pool gọi closure.
    
    
    $client = new Client();
    
    $requests = function ($total) use ($client) {
        $uri = 'http://127.0.0.1:8126/guzzle-server/perf';
        for ($i = 0; $i < $total; $i++) {
            yield function() use ($client, $uri) {
                return $client->getAsync($uri);
            };
        }
    };
    
    $pool = new Pool($client, $requests(100));
    

## Sử dụng các response

Trong các ví dụ trước , chúng ta đã lấy một biến `$response` hoặc chúng ta đã lấy được một response từ một promise. Đối tượng response bổ sung một chuẩn response PSR-7,`PsrHttpMessageResponseInterface`,và chứa một vài thông tin hữu ích.

Bạn có thể lấy mã trạng thái và  cụm từ kết luận của response:
    
    
    $code = $response->getStatusCode(); // 200
    $reason = $response->getReasonPhrase(); // OK   
    

Bạn có thể lấy headers từ response:
    
    
    // kiểm tra nếu header tồn tại
    if ($response->hasHeader('Content-Length')) {
        echo "It exists";
    }
    
    // Lấy một header từ response
    echo $response->getHeader('Content-Length');
    
    // Lấy tất cả các header response
    foreach ($response->getHeaders() as $name => $values) {
        echo $name . ': ' . implode(', ', $values) . "rn";
    }
    

Phần thân của một response có thể được lấy khi sử dụng phương thức `getBody`. Phần thân có thể được sử dụng như một chuỗi , ép kiểu như 1 chuỗi, hoặc được sử dụng như một stream giống đối tượng.
    
    
    $body = $response->getBody();
    // Implicitly cast the body to a string and echo it
    echo $body;
    // Explicitly cast the body to a string
    $stringBody = (string) $body;
    // Read 10 bytes from the body
    $tenBytes = $body->read(10);
    // Read the remaining contents of the body as a string
    $remainingBytes = $body->getContents();
    

## Chuỗi các tham số truy vấn

Bạn có thể cung cấp các chuỗi tham số truy vấn với một request theo nhiều cách.

Bạn có thể đặt nhiều chuỗi tham số truy vấn trong các request URI:
    
    
    $response = $client->request('GET', 'http://httpbin.org?foo=bar');
    

Bạn có thể chỉ định các chuỗi tham số truy vấn sử dụng thuộc tính request `query` như một mảng:
    
    
    $client->request('GET', 'http://httpbin.org', [
        'query' => ['foo' => 'bar']
    ]);
    

Cung cấp thuộc tính như một mảng sẽ sử dụng hàm  `http_build_query` của PHP để định dạng chuỗi truy vấn.

Và cuối cùng,bạn có thể cung cấp thuộc tính request `query` như một chuỗi.
    
    
    $client->request('GET', 'http://httpbin.org', ['query' => 'foo=bar']);
    

## Tải dữ liệu lên

Guzzle cung cấp nhiều phương thức cho việc tải dữ liệu lên.

Bạn có thể gửi các request có chứa một luồng dữ liệu  bằng cách  truyền một chuỗi,tài nguyên được trả lại từ `fopen` , hoặc một thực thể của một `PsrHttpMessageStreamInterface` từ thuộc tính request `body`.
    
    
    // cung cấp phần thân như 1 string.
    $r = $client->request('POST', 'http://httpbin.org/post', [
        'body' => 'raw data'
    ]);
    
    // cung cấp một tài nguyên fopen.
    $body = fopen('/path/to/file', 'r');
    $r = $client->request('POST', 'http://httpbin.org/post', ['body' => $body]);
    
    // sử dụng hàm stream_for() để tạo một luồng chuẩn PSR-7.
    $body = GuzzleHttpPsr7stream_for('hello!');
    $r = $client->request('POST', 'http://httpbin.org/post', ['body' => $body]);
    

Một cách đơn giản nhất để tải lên dữ liệu  JSON và đặt  header thích hợp là sử dụng tham số request `json`:
    
    
    $r = $client->request('PUT', 'http://httpbin.org/put', [
        'json' => ['foo' => 'bar']
    ]);
    

### POST/Form Requests

Một phần mở rộng để chỉ định dữ liệu thô của một request sử dụng thuộc tính request `body`,Guzzle cung cấp các tóm tắt hữu ích hơn việc gửi dữ liệu POST.

#### Gửi các trường của form

Gửi request POST `application/x-www-form-urlencoded` yêu cầu bạn chỉ định các trường POST như một mảng trong thuộc tính request `form_params`.
    
    
    $response = $client->request('POST', 'http://httpbin.org/post', [
        'form_params' => [
            'field_name' => 'abc',
            'other_field' => '123',
            'nested_field' => [
                'nested' => 'hello'
            ]
        ]
    ]);
    

#### Gửi các file của form

Bạn có thể gửi các file cùng với một form  (`multipart/form-data` POST requests) , sử dụng thuộc tính request `multipart`.`multipart` chấp nhận một mảng của các mảng liên kết , nơi mà mỗi mảng liên kết chứa các khóa sau:

* name: (required, string) ánh xạ khóa tới tên trường biểu mẫu.
* contents: (required, mixed) cung cấp một chuỗi để gửi nội dung của tệp dưới dạng chuỗi, cung cấp tài nguyên fopen để truyền nội dung từ luồng PHP hoặc cung cấp `PsrHttpMessageStreamInterface` để truyền nội dung từ luồng PSR-7.
    
    
    $response = $client->request('POST', 'http://httpbin.org/post', [
        'multipart' => [
            [
                'name'     => 'field_name',
                'contents' => 'abc'
            ],
            [
                'name'     => 'file_name',
                'contents' => fopen('/path/to/file', 'r')
            ],
            [
                'name'     => 'other_file',
                'contents' => 'hello',
                'filename' => 'filename.txt',
                'headers'  => [
                    'X-Foo' => 'this is an extra header to include'
                ]
            ]
        ]
    ]);
    

## Cookies

Guzzle có thể duy trì một phiên cookie dành cho bạn nếu được hướng dẫn sử dụng tùy chọn request `cookie`. Khi gửi request, tùy chọn `cookie` phải được đặt thành một thể hiện của `GuzzleHttpCookieCookieJarInterface`.
    
    
    // Use a specific cookie jar
    $jar = new GuzzleHttpCookieCookieJar;
    $r = $client->request('GET', 'http://httpbin.org/cookies', [
        'cookies' => $jar
    ]);
    

Bạn có thể đặt cookie thành true trong một trình tạo client nếu bạn muốn sử dụng một cookie jar được chia sẻ cho tất cả các request.
    
    
    // Use a shared client cookie jar
    $client = new GuzzleHttpClient(['cookies' => true]);
    $r = $client->request('GET', 'http://httpbin.org/cookies');
    

## Các điều hướng

Guzzle sẽ tự động theo các chuyển hướng trừ khi bạn không bảo nó. Bạn có thể tùy chỉnh hành vi chuyển hướng bằng cách sử dụng tùy chọn request allow_redirects.

* Đặt thành true để bật chuyển hướng bình thường với số lượng tối đa là 5 chuyển hướng. Đây là thiết lập mặc định.
* Đặt thành false để tắt chuyển hướng.
* Chuyển mảng kết hợp chứa khóa 'max' để chỉ định số lượng chuyển hướng tối đa và tùy chọn cung cấp giá trị khóa 'nghiêm ngặt' để chỉ định có sử dụng chuyển hướng tuân thủ RFC nghiêm ngặt hay không (nghĩa là request POST chuyển hướng có request POST so với thực hiện các trình duyệt thực hiện chuyển hướng các yêu cầu POST với các request GET).
    
    
    $response = $client->request('GET', 'http://github.com');
    echo $response->getStatusCode();
    // 200
    

Ví dụ sau cho thấy các chuyển hướng có thể bị tắt.
    
    
    $response = $client->request('GET', 'http://github.com', [
        'allow_redirects' => false
    ]);
    echo $response->getStatusCode();
    // 301
    

## Các ngoại lệ

Guzzle ném ngoại lệ cho các lỗi xảy ra trong quá trình chuyển.

* Trong trường hợp xảy ra lỗi mạng (thời gian chờ kết nối, lỗi DNS, v.v.), một GuzzleHttpExceptionRequestException sẽ bị ném. Ngoại lệ này kéo dài từ GuzzleHttpExceptionTransferException. Bắt ngoại lệ này sẽ bắt bất kỳ ngoại lệ nào có thể được ném trong khi chuyển request.
    
        use GuzzleHttpPsr7;
    use GuzzleHttpExceptionRequestException;
    
    try {
        $client->request('GET', 'https://github.com/_abc_123_404');
    } catch (RequestException $e) {
        echo Psr7str($e->getRequest());
        if ($e->hasResponse()) {
            echo Psr7str($e->getResponse());
        }
    }
    

* Một ngoại lệ GuzzleHttpExceptionConnectException được ném trong trường hợp có lỗi mạng. Ngoại lệ này kéo dài từ GuzzleHttpExceptionRequestException.
* Một GuzzleHttpExceptionClientException được ném cho 400 lỗi cấp nếu tùy chọn yêu cầu http_errors được đặt thành true. Ngoại lệ này kéo dài từ GuzzleHttpExceptionBadResponseException và GuzzleHttpExceptionBadResponseException kéo dài từ GuzzleHttpExceptionRequestException.
    
        use GuzzleHttpExceptionClientException;
    
    try {
        $client->request('GET', 'https://github.com/_abc_123_404');
    } catch (ClientException $e) {
        echo Psr7str($e->getRequest());
        echo Psr7str($e->getResponse());
    }
    

* Một GuzzleHttpExceptionServerException được ném cho 500 lỗi cấp nếu tùy chọn yêu cầu http_errors được đặt thành true. Ngoại lệ này kéo dài từ GuzzleHttpExceptionBadResponseException.
* Một GuzzleHttpExceptionTooManyRedirectsException được ném khi có quá nhiều chuyển hướng được theo sau. Ngoại lệ này kéo dài từ GuzzleHttpExceptionRequestException.

Tất cả các ngoại lệ trên đều kéo dài từ  `GuzzleHttpExceptionTransferException`.

## Các biến môi trường

Guzzle cho thấy một vài biến môi trường có thể được sử dụng để tùy chỉnh hành vi của thư viện.

`GUZZLE_CURL_SELECT_TIMEOUT`
: Kiểm soát thời lượng tính bằng giây mà một trình xử lý curl_multi_ * sẽ sử dụng khi chọn trên các handler curl bằng cách sử dụng curl_multi_select (). Một số hệ thống gặp sự cố với việc triển khai curl_multi_select () của PHP khi việc gọi chức năng này luôn dẫn đến việc chờ thời lượng chờ tối đa.

`HTTP_PROXY`
: 

Xác định proxy để sử dụng khi gửi request bằng giao thức “http”.

Lưu ý: vì biến HTTP_PROXY có thể chứa đầu vào người dùng tùy ý trên một số môi trường (CGI), biến này chỉ được sử dụng trên SAPI của CLI. Xem để biết thêm thông tin.

`HTTPS_PROXY`
: Xác định proxy để sử dụng khi gửi request bằng giao thức “https”.

[1]: http://docs.guzzlephp.org/overview.html#installation
[2]: http://tools.ietf.org/html/rfc3986#section-5.2
[3]: http://docs.guzzlephp.org/handlers-and-middleware.html
[4]: https://promisesaplus.com/
[5]: https://github.com/guzzle/promises

  
