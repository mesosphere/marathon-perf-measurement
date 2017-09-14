# Allegro Scale Tests

Ahoy there! So you want to run the allegro scale tests. Let's do this!

1) Create a virtual environment if you don't want to install the tool globally in your system:

    ```
    python3 -m venv ./virtualenv
    source virtualenv/bin/activate
    ```

2) Install the `dcos-perf-test-driver` (you might need your github credentials because it's a private repository):

    ```
    pip install git://github.com/mesosphere/dcos-perf-test-driver
    ```

3) Start the tests (I am assuming marathon is running locally. If not chance the URL accordingly):
    
    ```
    dcos-perf-test-driver ./johannes-allegro.yml -Dmarathon_url=http://127.0.0.1:8080
    ```    

4) Check the `allegro-endpointResponseTime.png`

## Documentation

Check the online [documentation](https://github.com/mesosphere/dcos-perf-test-driver/tree/master/docs)
