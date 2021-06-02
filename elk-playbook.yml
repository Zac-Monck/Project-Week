# Project-Week
project week repository
 name: Config ELK VM with Docker
  hosts: 10.2.0.5
  remote_user: azdmin
  become: true
  tasks:
    - name: Install docker.io
      apt:     
        name: docker.io
        state: present
        update_cache: yes
    - name: Install pip3
      apt:
        name: python3-pip
        state: present
        force_apt_get: yes
    - name: Install docker via pip
      pip:
        name: docker
        state: present
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: '262144'
        state: present
        reload: yes
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        state: started
        restart_policy: always
        # Please list the ports that ELK runs on
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044
