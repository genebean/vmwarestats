# VMwareStats

This Vagrant box will quickly give you a stats collecting setup for VMware.
To use it all you need to do is copy `vsphere-influxdb-go.json.sample` to
`vsphere-influxdb-go.json`, edit these lines to include your site's vCenter(s)
and any accont that has at least read-only rights, and run `vagrant up`. Be sure
the last line in this array _does not_ have a trailing comma as that make JSON
sad.

```json
"VCenters": [
  { "Username": "your-username-here", "Password": "your-password-here", "Hostname": "vcenter1.example.com" },
  { "Username": "your-username-here", "Password": "your-password-here", "Hostname": "vcenter2.example.com" }
],
```

`vagrant up` sets up:

- InfluxDB
- [Oxalide/vsphere-influxdb-go](https://github.com/Oxalide/vsphere-influxdb-go)
- Grafana + some dashboards
- Telegraf for the local Vagrant vm

The Grafana database is seeded with the contents of `grafana.db`. If you make
changes that you want to save be sure to backup `/var/lib/grafana/grafana.db`
before destroying your vm.

## License

My code here is released under the included BSD 3 clause license. The component
software used by this project each has its own license.
