import { useState } from 'react'
import { Button } from "/components/ui/button"
import { Card, CardContent, CardHeader, CardTitle } from "/components/ui/card"
import { Input } from "/components/ui/input"
import { Label } from "/components/ui/label"
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select"
import { Textarea } from "@/components/ui/textarea"
import { RadioGroup, RadioGroupItem } from "/components/ui/radio-group"

type Ticket = {
  id: number
  title: string
  description: string
  priority: string
  status: string
}

export default function ServiceRequestTicket() {
  const [tickets, setTickets] = useState<Ticket[]>([])
  const [title, setTitle] = useState('')
  const [description, setDescription] = useState('')
  const [priority, setPriority] = useState('Low')
  const [statusFilter, setStatusFilter] = useState('All')

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault()
    if (!title || !description) return

    const newTicket: Ticket = {
      id: Date.now(),
      title,
      description,
      priority,
      status: 'Open',
    }

    setTickets([...tickets, newTicket])
    setTitle('')
    setDescription('')
    setPriority('Low')
  }

  const updateTicketStatus = (id: number, newStatus: string) => {
    setTickets(tickets.map(ticket => (ticket.id === id ? { ...ticket, status: newStatus } : ticket)))
  }

  const filteredTickets = tickets.filter(ticket => {
    if (statusFilter === 'All') return true
    return ticket.status === statusFilter
  })

  return (
    <div className="p-4">
      <Card className="mb-4">
        <CardHeader>
          <CardTitle>Create Service Request</CardTitle>
        </CardHeader>
        <CardContent>
          <form onSubmit={handleSubmit}>
            <div className="mb-4">
              <Label htmlFor="title" className="block mb-2">
                Title
              </Label>
              <Input
                id="title"
                value={title}
                onChange={(e) => setTitle(e.target.value)}
                className="w-full"
                required
              />
            </div>
            <div className="mb-4">
              <Label htmlFor="description" className="block mb-2">
                Description
              </Label>
              <Textarea
                id="description"
                value={description}
                onChange={(e) => setDescription(e.target.value)}
                className="w-full"
                required
              />
            </div>
            <div className="mb-4">
              <Label htmlFor="priority" className="block mb-2">
                Priority
              </Label>
              <Select onValueChange={(value) => setPriority(value)} defaultValue="Low">
                <SelectTrigger className="w-full">
                  <SelectValue placeholder="Select priority" />
                </SelectTrigger>
                <SelectContent>
                  <SelectItem value="Low">Low</SelectItem>
                  <SelectItem value="Medium">Medium</SelectItem>
                  <SelectItem value="High">High</SelectItem>
                </SelectContent>
              </Select>
            </div>
            <Button type="submit" className="w-full">
              Submit
            </Button>
          </form>
        </CardContent>
      </Card>

      <Card>
        <CardHeader>
          <CardTitle>Service Requests</CardTitle>
          <div className="mt-2">
            <Label htmlFor="status-filter" className="block mb-2">
              Filter by Status
            </Label>
            <Select onValueChange={(value) => setStatusFilter(value)} defaultValue="All">
              <SelectTrigger className="w-full">
                <SelectValue placeholder="Select status" />
              </SelectTrigger>
              <SelectContent>
                <SelectItem value="All">All</SelectItem>
                <SelectItem value="Open">Open</SelectItem>
                <SelectItem value="In Progress">In Progress</SelectItem>
                <SelectItem value="Closed">Closed</SelectItem>
              </SelectContent>
            </Select>
          </div>
        </CardHeader>
        <CardContent>
          <div className="space-y-4">
            {filteredTickets.map(ticket => (
              <div key={ticket.id} className="border p-4 rounded">
                <div className="flex justify-between items-center">
                  <div>
                    <h3 className="text-lg font-bold">{ticket.title}</h3>
                    <p className="text-gray-600">{ticket.description}</p>
                    <p className="text-gray-500">Priority: {ticket.priority}</p>
                  </div>
                  <div>
                    <p className="text-gray-500">Status: {ticket.status}</p>
                    <RadioGroup
                      defaultValue={ticket.status}
                      onValueChange={(value) => updateTicketStatus(ticket.id, value)}
                    >
                      <div className="flex items-center space-x-2">
                        <RadioGroupItem value="Open" id={`open-${ticket.id}`} />
                        <Label htmlFor={`open-${ticket.id}`}>Open</Label>
                      </div>
                      <div className="flex items-center space-x-2">
                        <RadioGroupItem value="In Progress" id={`in-progress-${ticket.id}`} />
                        <Label htmlFor={`in-progress-${ticket.id}`}>In Progress</Label>
                      </div>
                      <div className="flex items-center space-x-2">
                        <RadioGroupItem value="Closed" id={`closed-${ticket.id}`} />
                        <Label htmlFor={`closed-${ticket.id}`}>Closed</Label>
                      </div>
                    </RadioGroup>
                  </div>
                </div>
              </div>
            ))}
          </div>
        </CardContent>
      </Card>
    </div>
  )
}
