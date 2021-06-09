name: Development

on: [push, pull_request]

jobs:
  test:
    runs-on: ${{matrix.os}}-latest
    continue-on-error: ${{matrix.experimental}}
    
    strategy:
      matrix:
        os:
          - ubuntu
        
        ruby:
          - 2.5
          - 2.6
          - 2.7
        
        experimental: [false]
        env: [""]
    
    steps:
    - uses: actions/checkout@v2
    - uses: ruby/setup-ruby@v1
      with:
        ruby-version: ${{matrix.ruby}}
    
    - name: Install dependencies
      run: ${{matrix.env}} bundle install
    
    - name: Run tests
      timeout-minutes: 5
      env:
        CLOUDFLARE_TEST_ZONE_MANAGEMENT: true
        CLOUDFLARE_EMAIL: ${{secrets.CLOUDFLARE_EMAIL}}
        CLOUDFLARE_KEY: ${{secrets.CLOUDFLARE_KEY}}
      run: ${{matrix.env}} bundle 
